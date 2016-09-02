**Build issues? File a bug at code.google.com/p/v8/issues or ask for help on v8-users@googlegroups.com.**

# Building V8

V8 is built with the help of [GN](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs). GN is a meta build system of sorts, as it generates build files for a number of other build systems. How you build therefore depends on what "back-end" build system and compiler you're using.
The instructions below assume that you already have a [checkout of V8](Using Git) but haven't yet installed the build dependencies.

If you intend to develop on V8, i.e., send patches and work with changelists, you will need to install the dependencies as described [here](Using Git).

More information on GN can be found in [Chromium's documentation](https://www.chromium.org/developers/gn-build-configuration) or [GN's own](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs).

## Prerequisite: Build dependencies

All build dependencies are fetched by running:

```gclient sync```

GN itself is distributed with [depot_tools](https://www.chromium.org/developers/how-tos/install-depot-tools).

## Building

There are two workflows for building v8. A raw workflow using commands on a lower level and a convenience workflow using wrapper scripts.

#### Build instructions (convenience workflow) 

Use a convenience script to generate your build files, e.g.:

```tools/dev/v8gen.py x64.release```

Call ```v8gen.py --help``` for more information. You can add an alias ```v8gen``` calling the script and also use it in other checkouts.

List available configurations (or bots from a master):

```tools/dev/v8gen.py list```

```tools/dev/v8gen.py list -m client.v8```

Build like a particular bot from waterfall client.v8 in folder foo:

```tools/dev/v8gen.py -b "V8 Linux64 - debug builder" -m client.v8 foo```

#### Build instructions (raw workflow) 

First, generate the necessary build files:

```gn args out.gn/foo```

An editor will open for specifying the [gn arguments](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs/reference.md). You can replace ```foo``` with an arbitrary directory name.  Note that due to the conversion from gyp to gn, we use a separate ```out.gn``` folder, to not collide with old gyp folders. If you don't use gyp or keep your subfolders separate, you can also use ```out```.

You can also pass the arguments on the command line:

```gn gen out.gn/foo --args='is_debug=false target_cpu="x64" v8_target_cpu="arm64" use_goma=true'```

This will generate build files for compiling V8 with the arm64 simulator in release mode using goma for compilation. For an overview of all available gn arguments run:

```gn args out.gn/foo --list```

## Compilation

For building all of V8 run (assuming gn generated to the ```x64.release``` folder):

```ninja -C out.gn/x64.release```

To build specific targets like d8, add them to the command line:

```ninja -C out.gn/x64.release d8```

## Testing

You can pass the output directory to the test driver. Other relevant flags will be inferred from the build:

```tools/run-tests.py --outdir out.gn/foo```

You can also test your most recently compiled build (in ```out.gn```):

```tools/run-tests.py --gn```
