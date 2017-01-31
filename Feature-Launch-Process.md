*Version 2, October 8th, 2014*

The V8 project aims to develop a high-performance, standards-compliant ECMAScript/JavaScript implementation. This document outlines our guidelines for "language-facing" changes and the process through which they are enforced.

# Guidelines

We strive to be responsible stewards of JavaScript language, balancing interoperability and innovation. Our guidelines align closely with those of [Blink project](http://www.chromium.org/blink#new-features). Keep in mind that these are directional beacons, not bright-line rules.

## Compatibility Risk

Factors that reduce compatibility risk include:
- **Acceptance at TC39 committee**. TC39 is a primary steward of JavaScript language. We track progress of language features through the [TC-39 Process](https://docs.google.com/a/chromium.org/document/d/1QbEE0BsO4lvl7NFTn5WXWeiEIBfaVUF7Dk0hpPpPDzU/) and consider features that reach higher stages more stable and ready for implementation. The V8 team is actively involved in the TC39 committee and champions new features when appropriate.
- **Interest from other browser vendors**. Implementations in other browsers are a clear signal of feature usefulness. In order of strength, this includes:
  1. compatible implementation in more than one engine
  2. compatible implementation in one engine
  3. implementation in one or more engines under an experimental flag
  4. other vendors expressed interest in the feature

## Impact on Web Platform

In prioritizing the work of implementing new features, we will prefer those which unblock significant new **capability**, **performance** or **expressiveness**.

For every change we implement we want to validate our design and implementation every step of the way. As such, we aim to have a **robust set of use cases** for new features we implement; ideally, we want to be involved with the community, identifying **groups of developers ready and willing to provide feedback** for our implementations.

## Technical Considerations

From day one, V8 has been all about performance. We strive to set standards for JavaScript performance across browsers, both by implementing advanced optimizations and by building robust benchmarks in our [Octane benchmarking suite](http://chromium.github.io/octane/).

From a compatibility risk perspective, bugs and inadvertent incompatibilities in language implementations are very painful for users. Therefore we take special care to ensure high-quality implementations of JavaScript features we ship.

With this in mind, we expect all JavaScript changes implemented in V8 to be accompanied by:

1. **Assessment of the impact on the codebase**. V8 is a highly complex and tightly knit codebase with many interdependencies; support cost for particularly pervasive features might be substantial.
2. **Conformance tests**, ideally suitable for future inclusion in [ECMA-262 test suite](https://github.com/tc39/test262).
3. **Performance tests**.

As the implementation of a particular feature progresses, we expect both conformance and performance test suites to progress in parallel.

# Process

Language features implemented in V8 go over 3 stages: experimental implementation, staging, and shipping without a flag.

## Experimental implementation

Anyone who wants to implement a feature in V8 must contact [v8-users@googlegroups.com](v8-users@googlegroups.com) with an intent-to-implement e-mail. Then follow these steps:

- Clarify the feature status with regard to the criteria in the guidelines on this page (TC39 acceptance, interest from browser vendors, testing plans) in an intent-to-implement e-mail.
- Provide a design doc to clarify V8 code base impact.
- The implementation should also consider DevTools support.
- Implement the feature under V8 "--harmony-X" flag.
- Develop conformance and performance tests in parallel.

## Staging

At this stage, the feature becomes available in V8 under “--es-staging” flag. The criteria for moving a feature to that stage are:

- The specification of the feature is stable
- One example of feature stability indicator is it being advanced to stage 3 of the [TC39 process](https://docs.google.com/a/chromium.org/document/d/1QbEE0BsO4lvl7NFTn5WXWeiEIBfaVUF7Dk0hpPpPDzU/)
- Implementation is mostly complete; remaining issues are identified and documented.
- Conformance tests are in place
- Performance regression tests are in place

## Turning the flag on - shipping feature to the open Web

As the implementation of a feature progresses, we will evaluate community feedback on feature design and implementation. V8 team will make a decision to turn the feature on by default based on community opinion of the feature and technical maturity of implementation.

Some community signals we will consider before shipment:

- **The feature is on the clear track to standardization at TC39**. For example, a feature spec is available and has been through several rounds of reviews, or a feature is at Stage 4 of TC39 Process.
- **There is a clear interest in the feature from other browser vendors.** For example, another engine is shipping compatible implementation in experimental or stable channel.

The following technical criteria must be met for shipping:

1. Implementation is complete; feedback received from staged implementation is addressed.
2. No technical debt: V8 team is satisfied with the feature’s implementation quality (including basic DevTools support)
3. **Performance** is consistent with our high-performance goals.