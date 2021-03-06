# Cross-compiling with GN

First, make sure you can [build with GN](Building with GN).

Then, add android to your `.gclient` configuration file.

    target_os = ['android']  # Add this to get Android stuff checked out.

The `target_os` field is a list, so if you're also building on unix it'll look like this:

    target_os = ['android', 'unix']  # multiple target oses

Run `gclient sync`, and you'll get a large checkout under `./third_party/android_tools`.

Enable developer mode on your phone or tablet, and turn on USB debugging, via instructions [here](https://developer.android.com/studio/run/device.html). Also, get the handy [adb](https://developer.android.com/studio/command-line/adb.html) tool on your path. It's in your checkout at `./third_party/android_tools/sdk/platform-tools`.

Use `v8gen.py` to generate an arm release or debug build:

    tools/dev/v8gen.py arm.release

Then run `gn args out.gn/arm.release' and make sure you have the following keys:

    target_os = "android"
    target_cpu = "arm"
    v8_target_cpu = "arm"
    is_component_build = false

The keys should be the same for debug builds. If you are building for an arm64 device like the Pixel C, which supports 32bit and 64bit binaries, the keys should look like this:

    target_os = "android"
    target_cpu = "arm64"
    v8_target_cpu = "arm64"
    is_component_build = false

Now build:

    ninja -C out.gn/arm.release d8

Using adb copy the binary and snapshot files to the phone:

    adb push out.gn/arm.release/d8 /data/local/tmp
    adb push out.gn/arm.release/natives_blob.bin /data/local/tmp
    adb push out.gn/arm.release/snapshot_blob.bin /data/local/tmp


    rebuffat:~/src/v8$ adb shell
    bullhead:/ $ cd /data/local/tmp
    bullhead:/data/local/tmp $ ls
    v8 natives_blob.bin snapshot_blob.bin
    bullhead:/data/local/tmp $ ./d8
    V8 version 5.8.0 (candidate)
    d8> 'w00t!'
    "w00t!"
    d8> 

# Building with Gyp

See [Building with Gyp](Building with Gyp) for more information on how to build.

# Using Sourcery G++ Lite

The Sourcery G++ Lite cross compiler suite is a free version of Sourcery G++ from [CodeSourcery](http://www.codesourcery.com). There is a page for the [GNU Toolchain for ARM Processors](http://www.codesourcery.com/sgpp/lite/arm). Determine the version you need for your host/target combination.

The following instructions uses [2009q1-203 for ARM GNU/Linux](http://www.codesourcery.com/sgpp/lite/arm/portal/release858), and if using a different version please change the URLs and `TOOL_PREFIX` below accordingly.

## Installing on host and target

The simplest way of setting this up is to install the full Sourcery G++ Lite package on both the host and target at the same location. This will ensure that all the libraries required are available on both sides. If you want to use the default libraries on the host there is no need the install anything on the target.

The following script will install in `/opt/codesourcery`:

```
#!/bin/sh

sudo mkdir /opt/codesourcery
cd /opt/codesourcery
sudo chown $USERNAME .
chmod g+ws .
umask 2
wget http://www.codesourcery.com/sgpp/lite/arm/portal/package4571/public/arm-none-linux-gnueabi/arm-2009q1-203-arm-none-linux-gnueabi-i686-pc-linux-gnu.tar.bz2
tar -xvf arm-2009q1-203-arm-none-linux-gnueabi-i686-pc-linux-gnu.tar.bz2
```