
**Quick links:** [browse](https://chromium.googlesource.com/v8/v8/) | [browse bleeding edge](https://chromium.googlesource.com/v8/v8/+/master) | [changes](https://chromium.googlesource.com/v8/v8/+log/master).

# Command-Line Access

## Git
See [[Using Git|Using Git]].

# Source Code Branches

There are several different branches of V8; if you're unsure of which version to get, you most likely want the up-to-date stable version. Have a look at our [Release Process](Release Process) for more information about the different branches used.

You may want to follow the V8 version that Chrome is shipping on its stable (or beta) channels, see http://omahaproxy.appspot.com.

# V8 public API compatibility

V8 public API (basically the files under include/ directory) may change over time. New types/methods may be added without breaking existing functionality. When we decide that want to drop some existing class/methods, we first mark it with [V8\_DEPRECATED](https://code.google.com/p/chromium/codesearch#search/&q=V8_DEPRECATED&sq=package:chromium&type=cs) macro which will cause compile time warnings when the deprecated methods are called by the embedder. We keep deprecated method for one branch and then remove it. E.g. if `v8::CpuProfiler::FindCpuProfile` was plain non deprecated in _3.17_ branch, marked as `V8_DEPRECATED` in _3.18_, it may well be removed in _3.19_ branch.

We also maintain a [document mentioning important API changes](https://docs.google.com/document/d/1g8JFi8T_oAE_7uAri7Njtig7fKaPDfotU6huOa1alds/edit) for each version. 
