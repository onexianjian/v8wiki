[D8](https://codesearch.chromium.org/chromium/src/v8/src/d8.h?q=d8&sq=package:chromium&l=5) (Debug8) is V8's own minimalist debug shell.

D8 is useful for running some JavaScript locally or debugging changes you have made to V8. A normal V8 build using [[GN|Building with GN]] for x64 will output a D8 binary in `out.gn/x64.optdebug/d8`. You can call D8 with the  `--help` argument for more information about usage and flags.

## Print to the Command Line

Printing output is probably going to be very important if you plan to use D8 to run JavaScript files, rather than interactively. This is very simple with the `print()` function:

```
--- test.js ---
print("Hello, World!");
```
```
out.gn/x64.optdebug/d8 test.js
> Hello, World!
```

## Read Input

Using `read()` you can store the contents of a file into a variable.
```
d8> var license = read("LICENSE");
d8> license
"This license applies to all parts of V8 that are not externally
maintained libraries.  The externally maintained libraries used by V8
are:
... (etc.) "
```

Use `readline()` to interactively enter text:
```
d8> var greeting = readline();
Welcome
d8> greeting
"Welcome"
```

## Load External Scripts
`load()` runs another JavaScript file in the current context, meaning that you can then access anything declared in that file.

```
--- util.js ---
function greet(name) {
  return "Hello, " + name;
}
```
```
d8> load('util.js');
d8> greet('World!');
"Hello, World!"
```

## Pass Flags Into JavaScript

It's possible to make command line arguments available to your JavaScript code at runtime with D8. Just pass them after `--` on the command line and you will be able to access them at the top level of your script using the `arguments` object.

```
out.gn/x64.optdebug/d8 -- hi
```
You can now access an array of the arguments using the `arguments` object:
```
d8> arguments[0]
"hi"
d8>
```

## More Resources
[Kevin Ennis' D8 Guide](https://gist.github.com/kevincennis/0cd2138c78a07412ef21) has really good information about exploring V8 using D8.