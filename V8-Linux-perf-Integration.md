# Introduction

V8 has built-in support for the Linux perf tool. By default, this support is disabled, but by using the --perf-prof and --perf-prof-debug-info command line options, V8 will write out performance data during execution into a file that can be used to analyze the performance of V8's JITted code with the Linux perf tool.

# Setup

In order to analyze V8 JIT code with the Linux perf tool you will need to:
- Use a very recent Linux kernel that provides high-resolution timing information to the perf tool and to V8's perf integration in order to synchronize JIT code performance samples with the standard performance data collected by the Linux perf tool.
- Use a recent very recent version of the Linux perf tool or apply the patch that supports JIT code to perf and build it yourself.

Install a new Linux kernel (will require a reboot):
```
sudo apt-get install linux-generic-lts-wily
```

Install dependencies:
```
sudo apt-get install libdw-dev libunwind8-dev
```

Download kernel sources that includes the latest perf tool source:
```
git clone git://git.kernel.org/pub/scm/linux/kernel/git/tip/tip.git
cd tip/tools/perf
make
```

# Build

To use V8's integration with Linux perf you need to build it with the appropriate GN build flag activated. You can set "enable_profiling = true" in an existing GN build configuration.

Alternatively, you create a new clean build configuration with only the single build flag set to enable perf support:
```
cd v8
tools/dev/v8gen.py x64.perf
echo "enable_profiling = true" >> out.gn/x64.perf/args.gn
```


