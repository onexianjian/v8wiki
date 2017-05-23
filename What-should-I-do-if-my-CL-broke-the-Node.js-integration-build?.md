## Background
For stability reasons, [Node master](https://github.com/nodejs/node) uses a V8 branch, that is at stable or older. For additional integration, the V8 team [builds](https://build.chromium.org/p/client.v8.fyi/builders/V8%20-%20node.js%20integration) Node with [V8 master](https://chromium.googlesource.com/v8/v8.git), i.e., with a V8 version that is several weeks newer than the V8 version in the official [Node master](https://github.com/nodejs/node). 

If the Node integration bot [Node integration build](https://build.chromium.org/p/client.v8.fyi/builders/V8%20-%20node.js%20integration) fails on our CQ, there is either a legitimate problem with your CL (you should revert the CL and fix it) or [Node](https://github.com/v8/node/) must be modified. If the tests failed, search for "Not OK" in the logfiles. **This document describes how to reproduce the problem locally and how to make changes to [V8’s Node fork](https://github.com/v8/node/) if your V8 CL causes the build to fail.**

*Note: Patches in V8’s fork are usually cherry-picked by the person who updates V8 in [Node](https://github.com/nodejs/node) (usually several weeks or month later). If you merged a fix to V8’s Node fork, there’s nothing else you need to do.*

# Reproduce locally 
Clone V8’s Node repository and check out the lkgr branch. 
```
git clone https://github.com/v8/node.git
```

If you already have a Node checkout, add v8/node as remote.

```
cd $NODE
git remote add v8-fork git@github.com:v8/node.git 
git checkout -b vee-eight-lkgr v8-fork/vee-eight-lkgr
```

Apply your patch, i.e., replace node/deps/v8 with a copy of v8 (lkgr branch) and build Node. 
```
$V8/tools/release/update_node.py $V8 $NODE
cd $NODE
./configure && make -j48 test
```
You can run single tests.
```
./node test/parallel/test-that-you-want.js
```
For debug builds, set v8_optimized_debug in common.gypi to true and run 
```
./configure --debug && make -j48 test
```

To run the debug binary, run `./node_g` rather than `./node`

# Make changes to Node.js
If you need to change something in [Node](https://github.com/v8/node/) so your CL doesn’t break the [build](https://build.chromium.org/p/client.v8.fyi/builders/V8%20-%20node.js%20integration) anymore, do the following. **You need a GitHub account for this**. 

### Get the Node sources
Fork [V8’s Node repository on Github](https://github.com/v8/node/) (click the fork button). Clone your Node repository and check out the lkgr branch. 
```
git clone git@github.com:your_user_name/node.git
```
If you already have a checkout of your fork of Node, you do not fork the repo. Instead, add v8/node as remote: 
```
cd $NODE
git remote add v8-fork git@github.com:v8/node.git 
git checkout -b vee-eight-lkgr v8-fork/vee-eight-lkgr
```
Make sure you have the correct branch and check that the current version builds and runs. Then create a new branch for your fix.
```
git checkout vee-eight-lkgr
./configure && make -j48 test
git checkout -b fix-something
```
### Apply your patch

Replace node/deps/v8 with a copy of v8 (lkgr branch) and build Node. 
```
$V8/tools/release/update_node.py $V8 $NODE
cd $NODE
./configure && make -j48 test
```
### Make fixes to Node

Make your changes to Node (not to deps/v8) and commit them.
```
git commit -m "subsystem: fix something"
```
*Note: if you make several commits, please squash them into one and format according to [Node’s guidelines](https://github.com/nodejs/node/blob/master/CONTRIBUTING.md#commit-guidelines). Github’s review works differently than V8 Chromium and your commit messages will end up in Node exactly how you wrote them locally (doesn’t matter what you type in the PR message). It’s OK to force push onto your fix-something branch.*

Build and run the tests again. Double check that your formatting looks like the rest of the file.
```
./configure && make -j48 test
make lint
git push origin fix-something
```
Once you have pushed the fixes to your repository, open a Pull Request on GitHub. This will send an email to the [V8 node-js team](https://github.com/orgs/v8/teams/node-js). They will review and merge your PR. If you have specific questions, ping the [V8 node-js team](https://github.com/orgs/v8/teams/node-js) maintainers.