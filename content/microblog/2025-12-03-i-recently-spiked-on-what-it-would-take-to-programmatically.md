---
title: "I recently spiked on what it would take to programmatically test the procedures…"
author: Dachary Carey
date: 2025-12-03 10:18:36 -0500
url: /microblog/2025/12/03/i-recently-spiked-on-what-it-would-take-to-programmatically/
topics: [ai, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7402003329880440832
draft: false
---

I recently spiked on what it would take to programmatically test the procedures in our documentation.

While that problem domain is super interesting and I will definitely post about it soon, today's takeaway is all about how incremental adoption of documentation platform enhancements can create technical debt that makes it harder to build new things.

We use reStructuredText, and we have three different ways we might structure procedures:

- Using a custom `procedure` directive
- Using .yaml "step" files that resolve to a procedure through our build system
- Using ordered lists

We *also* have two ways we might represent mutually exclusive procedure paths:

- Using a custom `tab` directive that often represents different programming languages or interfaces
- Using a custom `composable-tutorial` directive that works similarly, but has a different syntax and presentation

In each of these cases, we have added custom directives to simplify writer workflows but have not gone back and retrofitted the old content to use the new directives. In some cases, for technical reasons, but in other cases, for time-based reasons.

Additionally, we compose these options in inconsistent ways. Sometimes we contain an entire procedure *within* a mutually-exclusive path choice. And sometimes we use those mutually exclusive procedure path options inside a procedure *step*. So this represents two different implementations for each of those procedure path implementations.

In total, this represents 12 different variations of procedure implementations that any programmatic tooling must understand.

If we updated our old implementations to adopt the new platform features, that would drop to 2 variations if we only supported the formal `procedure` directive, or 4 variations if we also want to support ordered lists.

Incremental adoption enables teams to get started quickly with new functionality, but it also creates tech debt that slows down work if the old content is never updated.

In a world where we want AI to help us, having all these variations in implementation also makes it harder for AI to understand our docs structure and effectively complete tasks.

This has been an illuminating project for me in terms of exploring some types of tech debt that we rarely discuss in the documentation world.
