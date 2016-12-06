# Introduction

V8 has built-in support for the Linux perf tool. By default, this support is disabled, but by using the --perf-prof and --perf-prof-debug-info command line options, V8 will write out performance data during execution into a file that can be used to analyze the performance of V8's JITted code with the Linux perf tool.

# Setup

In order to analyze JIT code with the Linux perf tool, you will need to:
- Use a very recent Linux kernel or install a kernel module that provides high-resolution timing information to synchronize V8 JIT code performance data with the standard performance data collected by the Linux perf tool.
- Use a recent very recent version of the Linux perf tool or apply the patch that supports JIT code to perf and build it yourself.

Install a new Linux kernel (will require a reboot)
```
sudo apt-get install linux-generic-lts-wily
```
