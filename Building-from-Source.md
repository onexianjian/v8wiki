In order to be able to build V8 from scratch **on Linux/Mac for x64** please heed the following steps:

## Getting the V8 source TL;DR

More in-depth information can be found [here](https://github.com/v8/v8/wiki/Checking%20out%20source).

1. [Install Git](https://github.com/v8/v8/wiki/Using%20Git#prerequisites)

2. [Install `depot_tools`](https://www.chromium.org/developers/how-tos/install-depot-tools)

3. Update `depot_tools` by executing the following into your terminal/shell:

    ```sh
    gclient
    ```

4. Go into the directory where you want to download the V8 source into and execute the following in your terminal/shell:

    ```sh
    fetch v8
    cd v8
    ```

**Don’t simply `git clone` the V8 repository!**

## Building V8 TL;DR

More in-depth information can be found [here](https://github.com/v8/v8/wiki/Building%20with%20GN).

1. For macOS: install Xcode and accept its license agreement. (If you’ve installed the command-line tools separately, [remove them first](https://bugs.chromium.org/p/chromium/issues/detail?id=729990#c1).)

2. Make sure that you are in the V8 source directory. If you followed every step up until step 4 above, you’re already at the right location.

3. Download all the build dependencies by executing the following in your terminal/shell:

    ```sh
    gclient sync
    ```

4. Generate the necessary build files by executing the following in your terminal/shell:

    ```sh
    tools/dev/v8gen.py x64.release
    ```

5. Compile the source by executing the following in your terminal/shell:

    ```sh
    ninja -C out.gn/x64.release
    ```

6. Run the tests by executing the following in your terminal/shell:

    ```sh
    tools/run-tests.py --gn
    ```
