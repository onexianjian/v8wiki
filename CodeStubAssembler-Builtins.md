This document is intended as an introduction to writing CodeStubAssembler builtins, and is targeted towards V8 developers.

# Builtins

In V8, builtins can be seen as chunks of code that are executable by the VM at runtime. A common use case is to implement the functions of builtin objects (such as RegExp or Promise), but builtins can also be used to provide other internal functionality (e.g. as part of the IC system).

V8's builtins can be implemented using a number of different methods (each with different trade-offs):
* **Platform-dependent assembly language**: can be highly efficient, but need manual ports to all platforms and are difficult to maintain.
* **C++**: very similar in style to runtime functions and have access to V8's powerful runtime functionality, but usually not suited to performance-sensitive areas.
* **JavaScript**: concise and readable code, access to fast intrinsics, but frequent usage of slow runtime calls, subject to unpredictable performance through type pollution, and subtle issues around (complicated and non-obvious) JS semantics.
* **CodeStubAssembler**: provides efficient low-level functionality that is very close to assembly language while remaining platform-independent and preserving readability. 
