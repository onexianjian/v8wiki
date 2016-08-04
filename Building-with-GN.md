**Build issues? File a bug at code.google.com/p/v8/issues or ask for help on v8-users@googlegroups.com.**

# Building V8

V8 is built with the help of [GN](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs). GN is a meta build system of sorts, as it generates build files for a number of other build systems. How you build therefore depends on what "back-end" build system and compiler you're using.
The instructions below assume that you already have a [checkout of V8](using_git.md) but haven't yet installed the build dependencies.

If you intend to develop on V8, i.e., send patches and work with changelists, you will need to install the dependencies as described [here](using_git.md).

## Prerequisite: Installing GN

First, you need GYP itself. GYP is fetched together with the other dependencies by running:

```
gclient sync
```

## Building

### GCC + make

Requires GNU make 3.81 or later. Should work with any GCC >= 4.8 or any recent clang (3.5 highly recommended).

#### Build instructions

First, generate the necessary build files by running the following command in your V8 checkout.

```
gn gen out/Default
```

You can replace ```Default``` with the configuration of your choice e.g. ```Release``` or ```Debug```

For building V8 only as a library do:

```
ninja -C out/Default v8
```

If you want to build V8 with it's shell called D8 the command is

```
ninja -C out/Default d8
```

This 