You are working on a change. You're including in the change tests for the new code you've added. You want to verify the new code is indeed tested.

To simplify this task, you may want to evaluate code coverage. There are 2 ways of doing this - manual and automated.

# Manual

Relative to the root of the v8 repo, use `./tools/gcov.sh` (tested on linux). This will use gnu's code coverage tooling and some scripting to produce a html report, where you can drill down coverage info per directory, file, and then down to line of code. 

The script will build v8 under a separate out directory, using gcov settings. We use a separate directory to avoid clobbering your normal build settings. This separate directory is called `cov` - will be immediately under the repo root), and run the test suite, then produce the report. The path to the report is provided when the script completes.

If your change has architecture specific components, you can cumulatively collect coverage from architecture specific runs:

`./tools/gcov.sh x64 arm`

By default, the script collects from Release runs, if you want Debug, you may specify so:

`BUILD_TYPE=Debug ./tools/gcov.sh x64 arm arm64`

Running the script with no options will provide a summary of options as well.

# Automatic
For each change that landed, we run a x64 coverage analysis - see the [coverage bot](https://build.chromium.org/p/client.v8/builders/V8%20Linux64%20-%20gcov%20coverage).

To get the report for a particular run, you want to list the build steps, find the "gsutil coverage report" one (towards the end), and open the "report" under it.