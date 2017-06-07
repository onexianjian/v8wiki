V8 version numbers are of the form `x.y.z.w`, where:

- `x.y` is the Chromium milestone divided by 10 (e.g. M60 → `6.0`)
- `z` is automatically bumped whenever there’s a new [LKGR](https://www.chromium.org/glossary#TOC-Acronyms) (typically a few times per day)
- `w` is bumped for manually backmerged patches after a branch point

If `w` is `0`, it’s omitted from the version number. E.g. v5.9.211 (instead of “v5.9.211.0”) gets bumped up to v5.9.211.1 after backmerging a patch.

----

**Related:** [Which V8 version should I use?](https://gist.github.com/domenic/aca7774a5d94156bfcc1)