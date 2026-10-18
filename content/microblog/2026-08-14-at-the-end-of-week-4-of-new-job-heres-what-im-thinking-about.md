---
title: "At the end of Week 4 of New Job, here's what I'm thinking about"
author: Dachary Carey
date: 2026-08-14 07:49:56 -0400
url: /microblog/2026/08/14/at-the-end-of-week-4-of-new-job-heres-what-im-thinking-about/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7493997332783419392
draft: false
---

At the end of Week 4 of New Job, here's what I'm thinking about: agents don’t use your product like humans do, OR how you expect them to.

It’s the age old “you design a product to do X, user does Y, Z” - but for fun, agents also do “L, and something that looks like N-dimensional Foo.” It’s wild watching what agents decide to do.

For example, I’m working with a team that expected an agent to use a library by either:
- Running a provided Python file
- Running a provided tool

Instead, agents mostly:
- Wrote and executed their own Python files
- Wrote Python in a shell script

If you’re evaluating a trajectory to decide what “good” looks like, it would be easy to look only for the former and entirely miss the latter. And if you supply build scripts and tools, agents may never actually use them.

In another example, I was exploring how different interventions could improve agent accuracy and performance. I wanted to test each of these conditions in isolation, and then check some together to see what the agent would reach for when multiple options were available:
- Baseline (isolated, no augmentation available)
- Existing documentation
- Modified documentation
- A custom CLI
- A JSON file with machine-readable content

I disabled harness web fetch tools for the baseline test; one harness mostly failed, but another suspiciously got the task right every time. On inspection, it was working around the disabled web fetch tool by importing Python’s urllib and fetching documentation that way. So I *thought* I had designed a no-docs test, and it mostly worked for one harness, but a different agent worked around it.

If you’re designing agent experiences without testing how agents use your product in practice, you may be designing for a case that never happens. Agents use your product in weirder ways than you’d expect. And when you’re trying to isolate conditions for evals, don’t forget to check for how agents work around your constraints.
