---
title: "Ok, LinkedIn network, help me crowdsource from your lovely smart brains!"
author: Dachary Carey
date: 2026-05-12 09:11:48 -0400
url: /microblog/2026/05/12/ok-linkedin-network-help-me-crowdsource-from-your-lovely/
topics: [ai, agents, documentation, business]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7459953476874231808
draft: false
---

Ok, LinkedIn network, help me crowdsource from your lovely smart brains!

I know I've been posting a lot lately about agent-friendly docs and agent skills, but in the back of my mind, I've been percolating on what "Agent Experience" might look like as a dedicated role/persona within an org. I envision this as the bigger-picture ownership of how agents interact with our software products.

Something really stuck with me from the article [@talagluck](https://github.com/talagluck) posted about his Pizza API experiment: how the agents recover from failures. In his experiment, he observed that both Claude Code and Cursor fetched documentation once, failed to successfully write the API calls, but then didn't attempt to fetch docs again - they tried other recovery paths. Only Codex read the docs and explored thoroughly enough to successfully write the API calls without error. (I'll link to the article in the comments - so much interesting stuff there!)

BUT I have been thinking a lot about recovery paths. We *know* error messages are critical for developers, but we do a poor job with them anyway. That has been one of the most consistent sources of feedback I've seen over my documentation career - not enough details from/about errors and recommended handling/recovery paths. I can see this compounding quickly for agents, and can predict some of the consequences.

What I want from you: does anyone here know of any studies or best practices around agent failure recovery strategies? Any data behind what agents typically attempt to recover from failures, how successful they are, how much token spend it involves, what other mitigations agents reach for if they can't recover? I'm now thinking this would probably be a high impact area of improvement for Agent Experience, but would love to know what prior art may exist around this space, if any.

**Links from the comments:**

- <https://www.stateofdocs.com/research/do-ai-coding-agents-actually-read-your-docs>
