In order to be able to build V8 from scratch please heed the following steps:

# Getting the V8 source TL;DR;
More in-depth information can be found [here](https://github.com/v8/v8/wiki/Checking%20out%20source).

1.) [Install Git](https://github.com/v8/v8/wiki/Using%20Git#prerequisites)

2.) [Install depot_tools](https://www.chromium.org/developers/how-tos/install-depot-tools)

3.) Update depot_tools by typing the following into your terminal/shell
```
gclient
```
4.) Go into the directory where you want to download the V8 source into and type the following into your terminal/shell
```
fetch v8
cd v8
```
**Don't simply git-clone the V8 repository!**

# Building V8 TL;DR;
5.) Make sure that you are in the V8 source directory. If you followed every step up until step 4.) you already should be at the right location.

6.) Download all the build dependencies by executing the following in your terminal/shell
```
gclient sync
```
