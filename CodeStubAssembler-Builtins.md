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

## Declaring a new CSA Builtin

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