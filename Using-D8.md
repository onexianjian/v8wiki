[D8](https://codesearch.chromium.org/chromium/src/v8/src/d8.h?q=d8&sq=package:chromium&l=5) (Debug8) is V8's own minimalist debug shell.

D8 is useful for running some JavaScript locally or debugging changes you have made to V8. A normal V8 build using [GN](Building with GN) for x64 will output a D8 binary in out.gn/x64.optdebug/d8. You can call D8 with the  --help argument for more information about usage and flags.

## Pass Flags Into JavaScript

It's possible to make command line arguments available to your JavaScript code at runtime with D8. Just pass them after "--" on the command line and you will be able to access them at the top level of your script using the `arguments` object.

```
out.gn/x64.optdebug/d8 -- hi
```
You can now access an array of the arguments using the `arguments` object:
```
d8> arguments[0]
"hi"
d8>
```