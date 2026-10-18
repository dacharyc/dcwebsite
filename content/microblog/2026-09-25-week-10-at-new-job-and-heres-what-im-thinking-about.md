---
title: "Week 10 at New Job, and here's what I'm thinking about"
author: Dachary Carey
date: 2026-09-25 09:12:30 -0400
url: /microblog/2026/09/25/week-10-at-new-job-and-heres-what-im-thinking-about/
topics: [ai, agents, documentation, business]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7509238403083976704
draft: false
---

Week 10 at New Job, and here's what I'm thinking about: how does the vocabulary you use in your product affect agent outcomes?

We have some products where the terminology we use does not quite align with the terms that other products in the industry use. In some cases, we use the same term to mean something related but slightly different, while in other cases, we use a different term than the industry uses. This is independent of something else we do occasionally, which is use a term to mean different things in different documentation pages. 😬

Our PM and DX team has already identified that these terms are likely to be confusing to both humans and agents. But what we haven't been able to say is whether the term usage affects agent outcomes. We've also got three slightly different eval shapes to try to detail the term confusion, asking slightly different questions:

- Compare how this term as we use it relates to how specific other named software in the industry uses it; identify the conflicts
- Given the term, how would an LLM define it *without* our product knowledge, just from its baseline training data? Separately, given _our_ definition, how would an LLM name it? And what is the delta between our definition and the LLM baseline definition, and is it likely to cause real confusion?
- Given a task that relies on the agent understanding _our_ definition, does an agent fail to complete the task or otherwise struggle with it?

I'm really curious if anyone else has been digging into this, and if so, are there other vocab/terminology eval shapes you're using? And how often have you found the terminology actually shifts outcomes, versus being more "cosmetic"?

So far, I haven't yet been able to find an eval case where the terminology confusion actually affects agent outcomes in practice. I don't know if I just haven't found the eval case where this matters, or if agents are more adaptable to term confusion than we think.
