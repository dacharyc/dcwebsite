---
title: "I set out to write a script today to figure out how many instances of a given…"
author: Dachary Carey
date: 2025-10-17 18:25:34 -0400
url: /microblog/2025/10/17/i-set-out-to-write-a-script-today-to-figure-out-how-many/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7385078547272450048
draft: false
---

I set out to write a script today to figure out how many instances of a given thing we have on a certain docs page.

I paused before I started, thought a minute, and made it a CLI instead.

Now it has 3 commands with various flags that are going to be super useful for evaluating the impact of changes or scoping maintenance work across our docs. And I’m able to reuse some core logic in different ways across the different commands.

Scripts are nice for one-off tasks, but a lot of the problems we solve in docs aren’t one-off tasks.

The next time you’re considering writing a script, maybe make it a CLI instead.

**Links from the comments:**

- <https://github.com/mongodb/code-example-tooling/pull/85>
