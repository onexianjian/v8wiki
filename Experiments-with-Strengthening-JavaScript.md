# Introduction

The V8 team considers great JavaScript performance a pivotal aspect of the language implementation. However, over the years it has become increasingly challenging to optimize for the full range of JavaScript functionality that is found in the wild. At the same time, we don’t want to force developers to compromise usability just to stay on a narrow and tricky path to maximum performance.

With the advent of ECMAScript 6 (ES6) this year, JavaScript sees many significant improvements. Classes, modules, block scoping, iterators, default and rest parameters and the like can replace less nice alternatives. For newly-written JavaScript code, it is finally possible to retire some of the ["bad parts"](http://shop.oreilly.com/product/9780596517748.do) of the language. ... But by fully endorsing ES6, we can go even further!

Other aspects and properties of the language still make JavaScript development difficult at scale. JavaScript’s strength is its flexibility, but for larger applications, the cost of this flexibility can sometimes outweigh its benefits:

- **Performance**. Optimising JavaScript is hard. Contemporary VMs use runtime profiling and many smart heuristics to optimise dynamically — and they have gotten very good at it. Ironically, the smarter they get, the harder it becomes for programmers to *predict* performance. Falling off a performance cliff with seemingly innocent changes is common, and preventing it a black art.
- **Usability**. JavaScript comes with many surprises and complex implicit behaviours. Real errors can easily be masked by “sloppy” semantics, and for historical reasons, “fail soft” is the default. As a consequence, programming, debugging, testing, and maintenance tend to be more error-prone and brittle than necessary.
The V8 team has started to explore ways to mitigate these problems. Taking both ES6 and type systems for JavaScript (TypeScript, Closure Compiler, Flow) as starting points, we want to *experiment* with new language extensions to improve the developer experience and the scalability of the language — *directly in the VM*.

With the experience gained by these experiments we hope to be able to produce useful proposals for future versions of the ECMAScript standard.

# Strong Mode

We are looking into a new language mode (an extension of strict mode) that implements a *stronger* semantics by removing brittle or costly features. The goal is twofold:

- Avoid common pitfalls that have marginal practical purpose yet make writing safe code harder and debugging more difficult.
- Rule out behaviours that are known to cause significant performance problems in contemporary VMs (not just V8).
Strong mode hence not only should make development easier, it also codifies a reliable “*contract*” for predictable performance: it can guide programmers towards writing code that VMs can handle well (or in some cases, hopefully do in the future) and away from excessive optimisation/deoptimisation cycles due to unstable types.

Strong mode is purely a *subset* of JavaScript. It does not add any new features but focuses on *removing* some. It does so by turning some existing features (or certain uses thereof) into errors, either static or dynamic. *Any program running “correctly” in strong mode should run unchanged as ordinary JS*. Thus, strong mode programs can be executed on all VMs that can handle ES6, even ones that don’t recognise the mode.

Moreover, strong mode is fully interoperable with good old JavaScript, in the same manner that strict mode is. Strong functions can call "weak" functions or use "weak" objects, and vice versa.

Some examples of strong mode changes we are aiming for are: accessing missing properties throws, arrays cannot have holes, all scoping errors are static, classes are immutable. See [this presentation](https://drive.google.com/file/d/0B1v38H64XQBNT1p2XzFGWWhCR1k/view?usp=sharing) or the [strawman proposal](https://docs.google.com/document/d/1Qk0qC4s_XNCLemj42FqfsRLp49nDQMZ1y7fwf5YjaI4/view?usp=sharing) for details.

# SoundScript

We want to try implementing an optional type system for JavaScript, *directly in the VM*. The basis will be [TypeScript](http://www.typescriptlang.org/), but modified to have a *sound* semantics — i.e., types are always correct and cannot be subverted (though the type `any` exists as an escape hatch, making types optional).

Putting types into a VM opens up interesting *opportunities*:

- Programmers have types at their disposal directly in the *interactive* development environment.
- The VM can use type information for *early* and *aggressive* optimizations (many of which depend on soundness).
- A VM can implement necessary *runtime type checks* efficiently (thus enabling soundness).

However, implementing a user-facing type system directly in a JavaScript VM comes with a number of interesting *challenges*:

- Typing must be *efficient*, because compile-time is runtime.
- Type checking must support *lazy* compilation, i.e., be able to work one function at a time.
The type system must operate under an *"open world"* assumption, since new code can be loaded any time.
These are requirements that existing external type checkers like TypeScript, Closure Compiler, or Flow do not face. As the community moves forward to standardising a type system for JavaScript (there is significant momentum now), it is vital to address these issues. Besides optimisation opportunities, we hence view the SoundScript project as a testing ground for validating ideas for a JavaScript type system standard.

# Resources

This is an ongoing experiment. We have just started. Currently, our focus is on strong mode, which we have begun implementing in V8 and in Traceur.

Here are some useful resources if you want to *learn more*:

- a [presentation](https://drive.google.com/file/d/0B1v38H64XQBNT1p2XzFGWWhCR1k/view?usp=sharing) about the strong mode and SoundScript plans (strong mode still being called "sane mode" in that document)
- the current [strawman proposal](https://drive.google.com/file/d/0B1v38H64XQBNT1p2XzFGWWhCR1k/view?usp=sharing) for strong mode
- the [V8 meta issue](https://code.google.com/p/v8/issues/detail?id=3956) tracking strong mode implementation progress in V8
- the [Traceur branch](https://github.com/google/traceur-compiler/tree/strong-mode) implementing strong mode
- the [Google group](https://groups.google.com/forum/embed/?place=forum/strengthen-js) for discussing strong mode and SoundScript

If you want to *try out* strong mode, you can:

- run the d8 shell from the most recent V8 with the `--strong-mode` flag,
- run a [Chrome canary](https://www.google.com/chrome/browser/canary.html) from the command line with flag the *--js-flags="--strong-mode"*,
- or use [Traceur](https://github.com/google/traceur-compiler/tree/strong-mode) with strong mode extensions to emulate it on any VM.

However, be aware that this is work in progress. Most restrictions are not implemented yet.

Feedback is highly welcome! Please don't hesitate to post to the [Google group](https://groups.google.com/forum/embed/?place=forum/strengthen-js) or comment on the proposal on Github.

# Frequently Asked Questions

*Q: Is this forking JavaScript?*
A: No. Strong mode is specifically designed to be a subset of JavaScript. It does not add any new features or behaviors, it only turns some existing ones into errors. Also, strong mode can be used selectively, and it seamlessly integrates with ordinary JavaScript.

As for SoundScript, we want to stay as close to TypeScript as possible. Moreover, we are dedicated to work with other vendors to converge on a standard type system for JavaScript. This collaboration is already ongoing.

*Q: Will I be able to use strong mode or SoundScript in other browsers?*
A: Yes. We are working on two implementations in parallel: one in V8 and one in Traceur (a source-to-source compiler translating new JavaScript features into standard JavaScript). Moreover, “correct” strong mode programs should usually run unchanged in other browsers (though with less checking). With SoundScript, compilation will be required, of course; for performance reasons, generating runtime type checks will likely be optional in that case.

*Q: Will you propose this for the ECMAScript standard?*
A: Eventually we hope to be able to do so. At this stage, it is still an experiment, which we have presented at ECMA’s TC39. If it turns out to be successful, we would like to work with TC39 to turn it (or parts of it) into a standard.

*Q: How does SoundScript compare to asm.js?*
A: They have different goals: asm.js is a low-level language designed to be a compilation target for other (mostly low-level) languages. SoundScript is a user-facing type system for direct use of JavaScript as a high-level language. Both are complementary, both are useful, and V8 is committed to both.

*Q: Can I continue writing the same style of JavaScript in strong mode?*
A: That depends on what style you are writing. :) Strong mode tries to codify common and well-behaved JavaScript programming patterns, as they exist today, or as we expect them to emerge with ES6. It guides programmers along a well-lit path of reliable performance. Obviously, however, there are things you won’t be able to do, and a few patterns have to be replaced with something else. But if you need weaker semantics, you can still have that as well. Overall, we hope that strong mode strikes a reasonable balance between more reliable and scalable behaviour, and the flexibility of traditional JavaScript.

*Q: Are you turning JavaScript into Java?*
A: No. Strong mode is merely a mode you can opt into, and SoundScript is an optional type system. Conventional JavaScript will continue to be available. As a programmer, you have the choice.

*Q: How does strong mode relate to SoundScript?*
A: We do not know yet exactly. Typing will benefit substantially from strong mode restrictions, because they allow more precise types and require fewer fallbacks to the dynamic type any. But ideally we want a type system that works for all of JavaScript, even if the types are less precise in “weak” code. For starters, we will focus on typing for strong mode, though, because that is easier (yet still difficult enough).

*Q: Does this mean that V8 will stop optimising conventional JavaScript?*
A: No. We have two performance-related goals with strong mode and SoundScript: (1) Codify JavaScript programming patterns that are known to be efficient today — writing efficient JavaScript is a black art and a moving target; many developers have asked about guidelines, and strong mode is part of our answer. (2) Provide new opportunities for additional optimisations in the future — both a stronger semantics and a sound type system are enablers for that.

*Q: What is soundness?*
A: Soundness is a formal correctness property of a type system. It guarantees that (1) an expression that has a static type T can never evaluate to a value that is not a T, and that (2) evaluation cannot run into unexpected runtime type errors. SoundScript will be an optional type system, however. In such a system, runtime type errors can still occur in untyped code (more precisely, in code that deals with objects of dynamic type any). Nevertheless, where static types are used, they are known to be correct.

*Q: Why is soundness important?*
A: Like a stronger semantics, reliable type information implies substantial global invariants on the code. A compiler can exploit these for more informed, aggressive, non-local optimisations (such as choosing more efficient data representations), while avoiding ubiquitous runtime checks. Without soundness, types can merely be used as optimisation hints, but the generated code would still need to be able to back out. In particular, the most serious problem we see with JavaScript performance today would persist: the “opt/deopt cycle of death”, where optimisation assumptions are continuously invalidated. But soundness is also useful for programmers: it means that static type checking is actually reliable, such that certain runtime errors are guaranteed to not occur.

*Q: Isn’t sound typing difficult for JavaScript?*
A: Yes, very much so. It is an active research topic. SoundScript is an experiment. We don’t know yet how good we can make a type system that is sound, precise, usable, and flexible enough at the same time. However, research like [Safe TypeScript](http://research.microsoft.com/apps/video/default.aspx?id=226836) or work on Gradual Typing for other languages keeps us optimistic.

*Q: Why is lazy compilation important?*
A: It is what all JavaScript VMs do to keep latency down. In order to load web pages as quickly as possible, initial compilation skips over most function definitions, and only compiles them once they are called. This means that the VM has to be able to compile calls to a function before it has compiled the function itself. For type checking it means that the type system has to be able to know the type of a function without having to analyse its body.

*Q: Will that require writing more type annotations?*
A: If you are writing source code without additional tool support, yes. A type system adequate for JavaScript VMs will not be able to rely on extensive type inference, at least none that crosses function boundaries. You at least have to annotate function parameters and return types. On the other hand, you could still choose to go through an external tool like TypeScript, Closure Compiler or Flow: in the bright future of a typed JavaScript, they could do fancy type inference for you and decorate your program with the necessary type annotations.

*Q: Where can I learn more about SoundScript?*
A: Please bear with us. We are currently focusing on strong mode as a first step. There is no useful document describing SoundScript in more detail yet, only an overview [presentation](https://drive.google.com/file/d/0B1v38H64XQBNT1p2XzFGWWhCR1k/view?usp=sharing).

*Q: When can I use it?*
A: We have just started implementing strong mode in V8 and Traceur, and hope it to be available by the end of Q2. SoundScript is further out.

*Q: How can I contribute or criticize?*
A: Comment on the docs, try out the prototypes, share your opinion and experience on the mailing list! The more constructive feedback we get the better.