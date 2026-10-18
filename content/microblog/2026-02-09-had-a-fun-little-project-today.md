---
title: "Had a fun little project today"
author: Dachary Carey
date: 2026-02-09 18:27:04 -0500
url: /microblog/2026/02/09/had-a-fun-little-project-today/
topics: [ai, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7426768632074010625
draft: false
---

Had a fun little project today: I put together a proof-of-concept for evaluating AI code gen for a task related to our product across various LLMs with a handful of inputs:

- Baseline: “basic” prompt only, no help
- With docs: same “basic” prompt, plus read in the content from a relevant documentation page
- With Skill: same “basic” prompt, plus read in the relevant content from a targeted AI Skill

I’m trying to validate whether:

- Unassisted LLMs can successfully perform code gen tasks for our product
- Adding a relevant documentation page produces an improvement in the output
- Adding a targeted Skill produces an improvement in the output, and if so, how that compares with the “With docs” version

The results were… unexpected! There’s definitely going to be a blog post about this soon.
