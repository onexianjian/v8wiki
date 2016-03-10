This document introduces some key V8 concepts and provides a `hello world` example to get you started with V8 code.

## Audience

This document is intended for C++ programmers who want to embed the V8 JavaScript engine within a C++ application.

# Hello World

Let's look at a [Hello World example](https://chromium.googlesource.com/v8/v8/+/branch-heads/4.8/samples/hello-world.cc) that takes a JavaScript statement as a string argument, executes it as JavaScript code, and prints the result to standard out. 

- An isolate is a VM instance with its own heap.
- A local handle is a pointer to an object. All V8 objects are accessed using handles, they are necessary because of the way the V8 garbage collector works.
- A handle scope can be thought of as a container for any number of handles. When you've finished with your handles, instead of deleting each one individually you can simply delete their scope.
- A context is an execution environment that allows separate, unrelated, JavaScript code to run in a single instance of V8. You must explicitly specify the context in which you want any JavaScript code to be run.

These concepts are discussed in greater detail in the [Embedder's Guide](Embedder's Guide).

# Run the Example

Follow the steps below to run the example yourself:

1. Download the V8 source code and build V8 by following the [download](Checking out source) and [build](Building with Gyp) instructions.
  1. This hello world example is compatible with the version 4.8. You can check out this branch with `git checkout -b 4.8 -t branch-heads/4.8`.
  2. Build via `make x64.release` on a Linux x64 system to generate the correct binaries.
2. Copy the complete code from the previous section (the second code snippet), paste it into your favorite text editor, and save as `hello_world.cpp` in the V8 directory that was created during your V8 build.
3. Compile `hello_world.cpp`, linking to the static libraries created in the build process. For example, on 64bit Linux using the GNU compiler:

  ```
  g++ -I. hello_world.cpp -o hello_world -Wl,--start-group out/x64.release/obj.target/{tools/gyp/libv8_{base,libbase,external_snapshot,libplatform},third_party/icu/libicu{uc,i18n,data}}.a -Wl,--end-group -lrt -ldl -pthread -std=c++0x
  ```
4. V8 requires its 'startup snapshot' to run. Copy the snapshot files to where your binary is stored:
`cp out/x64.release/*.bin .`
5. Run the `hello_world` executable file at the command line.
For example, on Linux, still in the V8 directory, type the following at the command line:
`./hello_world`
6. You will see `Hello, World!`.

Of course this is a very simple example and it's likely you'll want to do more than just execute scripts as strings! For more information see the [Embedder's Guide](Embedder's Guide). If you are looking for an example which is in sync with master simply check out the file [`hello_world.cc`](https://chromium.googlesource.com/v8/v8/+/master/samples/hello-world.cc).