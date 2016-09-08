We continuously run [layout tests](https://www.chromium.org/developers/testing/webkit-layout-tests) on our [FYI waterfall](https://build.chromium.org/p/client.v8.fyi/console?branch=master) to prevent integration problems with Chromium.

On test failures, the bots compare the results of V8 ToT with Chromium's pinned V8 version, to only flag newly introduced V8 problems (with false positives <<5%). Blame assignment is trivial as the [linux release](https://build.chromium.org/p/client.v8.fyi/builders/V8-Blink%20Linux%2064) bot tests all revisions.

Commits with newly introduced failures are normally reverted to unblock auto-rolling into Chromium. In case you break layout tests and the changes are expected, follow this procedure:

  1. Land a Chromium-side change setting NeedsManualRebaseline for the changed tests (see [more](https://www.chromium.org/developers/testing/webkit-layout-tests/testexpectations#TOC-Rebaselining)).
  1. Land your V8 CL.
  1. Wait 1-2 days until your V8 CL has rolled into Chromium.
  1. Switch NeedsManualRebaseline to NeedsRebaseline on the Chromium-side. Test will be rebaselined automatically.

Please associate all CLs with a BUG.