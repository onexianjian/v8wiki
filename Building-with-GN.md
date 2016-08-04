**Build issues? File a bug at code.google.com/p/v8/issues or ask for help on v8-users@googlegroups.com.**

# Building V8

V8 is built with the help of [GN](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs). GN is a meta build system of sorts, as it generates build files for a number of other build systems. How you build therefore depends on what "back-end" build system and compiler you're using.
The instructions below assume that you already have a [checkout of V8](using_git.md) but haven't yet installed the build dependencies.

If you intend to develop on V8, i.e., send patches and work with changelists, you will need to install the dependencies as described [here](using_git.md).
