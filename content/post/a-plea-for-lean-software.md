+++
author = "Guowei"
categories = ["Thoughts"]
description = "Wisdom from professor Niklaus Wirth"
featured = ""
featuredpath = "/img"
title = "A Plea for Lean Software"
date = 2018-09-11T20:54:06+03:00
+++

I recently watched the talk *A Guide for the Perplexed* given by **Joe Armstrong**.

{{< youtube rmueBVrLKcY >}}

He recommended two papers. One is A [Plea for Lean Software](https://cr.yp.to/bib/1995/wirth.pdf) by **Niklaus Wirth**, who is the creator of the Pascal language.

After I read the paper I found that the this man is brilliant! He had found some of the deep problems in the industry and offered solutions decades ago. Unfortunately no one listened to him. Here are some of his wonderful words.

>What drives software towards complexity? A primary cause of complexity is that software vendors **uncritically adopt almost any feature that users want**. Any incompatibility with the original system concept is either ignored or passed unrecognized, which renders the design more complicated and its use more cumbersome.

>Another important reason for software complexity lies in **monolithic design**, wherein all conceivable features are part of the system's design.

>**To Some, complexity equals power**
>A system's ease of use always should be a primary goal, but that ease should be based on an underlying concept that makes the use almost intuitive. Increasingly, people seem to misinterpret complexity as sophistication, which is baffling - the incomprehensible should cause suspicion rather than admiration.

> Of course, a customer who pays - in advance - for service contracts is a more stable income source than a customer who has fully mastered a product's use. Industry and academia are probably pursuing very different goals; hence, the emergence of another law: **Customer dependence is more profitable than customer education.**

>**Time pressure is probably the foremost reason behind the emergence of bulky software.** The time pressure that designers endure discourages careful planning. It also discourages improving acceptable solutions; instead, it encourages quickly conceived software additions and corrections. Time pressure gradually corrupts an engineer's standard of quality and perfection. It has a detrimental effect on people as well as products.

>... an object-oriented language must embody strict, static typing that cannot be breached, whereby programmers can rely on the compiler to identify inconsistencies. Unfortunately, the most popular object-oriented language, C++, is no help here because it has been declared to be upwardly compatible with its ancestor C. Its wide acceptance confirms the following "laws": **Progress is acceptable only if it's compatible with the current state. Adhering to a standard is always safer.**

>**The belief that complex systems requires armies of designers and programmers is wrong.** A system that is not understood in its entirety, or at least to a significant degree of detail by a single individual, should probably not be built.

>**Reducing complexity and size must be the goal in every step** - in system specification, design, and in detailed programming.

>**To gain experience, there is no substitute for one's own programming effort.** Organizing a team into managers, designers, programmers, analysts, and users is detrimental.

> Programs should be written and polished until they acquire publication quality.


