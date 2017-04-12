This document is intended as an introduction to writing CodeStubAssembler builtins, and is targeted towards V8 developers.

# Builtins

In V8, builtins can be seen as chunks of code that are executable by the VM at runtime. A common use case is to implement the functions of builtin objects (such as RegExp or Promise), but builtins can also be used to provide other internal functionality (e.g. as part of the IC system).

V8's builtins can be implemented using a number of different methods (each with different trade-offs):
* **Platform-dependent assembly language**: can be highly efficient, but need manual ports to all platforms and are difficult to maintain.
* **C++**: very similar in style to runtime functions and have access to V8's powerful runtime functionality, but usually not suited to performance-sensitive areas.
* **JavaScript**: concise and readable code, access to fast intrinsics, but frequent usage of slow runtime calls, subject to unpredictable performance through type pollution, and subtle issues around (complicated and non-obvious) JS semantics.
* **CodeStubAssembler**: provides efficient low-level functionality that is very close to assembly language while remaining platform-independent and preserving readability.

The remaining document will focus on the latter and give a brief tutorial for developing a simple CodeStubAssembler (CSA) builtin exposed to JavaScript.

# CodeStubAssembler

V8's CodeStubAssembler is a custom, platform agnostic assembler that provides low-level primitives as a thin abstraction over assembly, but also offers an extensive library of higher-level functionality.

```
// Low-level:
// Loads the pointer-sized data at addr into value.
Node* addr = /* ... */;
Node* value = Load(MachineType::IntPtr(), addr);

// And high-level:
// Performs the JS operation ToString(object).
// ToString semantics are specified at https://tc39.github.io/ecma262/#sec-tostring. 
Node* object = /* ... */;
Node* string = ToString(context, object);
```

CSA builtins run through part of the TurboFan compilation pipeline (including block scheduling and register allocation, but notably not through optimization passes) which then emits the final executable code.

# Writing a CodeStubAssembler Builtin

In this section, we will write a simple CSA builtin to calculate n'th number of the Fibonacci sequence. Since we want it to remain efficient, we will only handle Smi (V8's internal small integer type) arguments and result. The builtin will be exposed to JS by installing it on the Math object (because we can).

This example demonstrates:
* Creating a CSA builtin with JavaScript linkage, which can be called like a JS function.
* Using CSA to implement simple logic: Smi and heap-number handling, conditionals, and calls to TFS builtins.
* Using CSA Variables.
* Installation of the CSA builtin on the Math object.

In case you'd like to follow along locally, the following code is based off revision [7a8d20a7](https://chromium.googlesource.com/v8/v8/+/7a8d20a79f9d5ce6fe589477b09327f3e90bf0e0).

## Declaring MathIs42

Builtins are declared in the `BUILTIN_LIST_BASE` macro in [src/builtins/builtins-definitions.h](https://cs.chromium.org/chromium/src/v8/src/builtins/builtins-definitions.h?q=builtins-definitions.h+package:%5Echromium$&l=1). To create a new CSA builtin with JS linkage and one parameter named 'X':

```
#define BUILTIN_LIST_BASE(CPP, API, TFJ, TFC, TFS, TFH, ASM, DBG)              \
  // [... snip ...]
  TFJ(MathIs42, 1, kX)                                                         \
  // [... snip ...]
```

Note that `BUILTIN_LIST_BASE` takes several different macros that denote different builtin kinds (see inline documentation for more details). CSA builtins specifically are split into:
* **TFJ**: JavaScript linkage.
* **TFS**: Stub linkage.
* **TFC**: Stub linkage builtin requiring a custom interface descriptor (e.g. if arguments are untagged or need to be passed in specific registers).
* **TFH**: Specialized stub linkage builtin used for IC handlers.

## Defining MathIs42

Builtin definitions are located in `src/builtins/builtins-*-gen.cc` files, roughly organized by topic. Since we will be writing a `Math` builtin, we'll put our definition into [src/builtins/builtins-math-gen.cc](https://cs.chromium.org/chromium/src/v8/src/builtins/builtins-math-gen.cc?q=builtins-math-gen.cc+package:%5Echromium$&l=1).

```
// TF_BUILTIN is a convenience macro that creates a new subclass of the given
// assembler behind the scenes.
TF_BUILTIN(MathIs42, MathBuiltinsAssembler) {
  // Load the current function context (an implicit argument for every stub)
  // and the X argument. Note that we can refer to parameters by the names
  // defined in the builtin declaration.
  Node* const context = Parameter(Descriptor::kContext);
  Node* const x = Parameter(Descriptor::kX);

  // At this point, x can be basically anything - a Smi, a HeapNumber,
  // undefined, or any other arbitrary JS object. Let's call the ToNumber
  // builtin to convert x to a number we can use.
  // CallBuiltin can be used to conveniently call any CSA builtin.
  Node* const number = CallBuiltin(Builtins::kToNumber, context, x);

  // Create a CSA variable to store the resulting value. The type of the
  // variable is kTagged since we will only be storing tagged pointers in it.
  VARIABLE(var_result, MachineRepresentation::kTagged);

  // We need to define a couple of labels which will be used as jump targets.
  Label if_issmi(this), if_isheapnumber(this), out(this);

  // ToNumber always returns a number. We need to distinguish between Smis
  // and heap numbers - here, we check whether number is a Smi and conditionally
  // jump to the corresponding labels.
  Branch(TaggedIsSmi(number), &if_issmi, &if_isheapnumber);

  // Binding a label begins generating code for it.
  BIND(&if_issmi);
  {
    // SelectBooleanConstant returns the JS true/false values depending on
    // whether the passed condition is true/false. The result is bound to our
    // var_result variable, and we then unconditionally jump to the out label.
    var_result.Bind(SelectBooleanConstant(SmiEqual(number, SmiConstant(42))));
    Goto(&out);
  }

  BIND(&if_isheapnumber);
  {
    // ToNumber can only return either a Smi or a heap number. Just to make sure
    // we add an assertion here that verifies number is actually a heap number.
    CSA_ASSERT(this, IsHeapNumber(number));
    // Heap numbers wrap a floating point value. We need to explicitly extract
    // this value, perform a floating point comparison, and again bind
    // var_result based on the outcome.
    Node* const value = LoadHeapNumberValue(number);
    Node* const is_42 = Float64Equal(value, Float64Constant(42));
    var_result.Bind(SelectBooleanConstant(is_42));
    Goto(&out);
  }

  BIND(&out);
  {
    Node* const result = var_result.value();
    CSA_ASSERT(this, IsBoolean(result));
    Return(result);
  }
}
```

## Attaching Math.Is42

Builtin objects such as `Math` are set up mostly in [src/bootstrapper.cc](https://cs.chromium.org/chromium/src/v8/src/bootstrapper.cc?q=src/bootstrapper.cc+package:%5Echromium$&l=1) (with some setup occurring in `.js` files). Attaching our new builtin is simple:

```
    // Existing code to set up Math, included here for clarity.
    Handle<JSObject> math = factory->NewJSObject(cons, TENURED);
    JSObject::AddProperty(global, name, math, DONT_ENUM);
    // [... snip ...]
    SimpleInstallFunction(math, "is42", Builtins::kMathIs42, 1, true);
```