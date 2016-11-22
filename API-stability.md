If V8 in a Chromium canary turns out to be crashy, it will get rolled back to the V8 version of the previous canary. It is therefore important to keep V8's API compatible from one canary version to the next.

We continuously run a [bot](https://build.chromium.org/p/chromium.fyi/builders/Linux%20V8%20API%20Stability) that signals API stability violations. It compiles Chromium's HEAD with V8's [current canary version](https://chromium.googlesource.com/v8/v8/+/refs/heads/canary).

Failures of this bot are currently only FYI and no action is required. The blame list can be used to easily identify dependent CLs in case of a rollback.

If you break this bot, be reminded to increase the window between a V8 change and a dependent Chromium change next time.