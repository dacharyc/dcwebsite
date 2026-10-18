---
title: "The Agent-Friendly Documentation Spec just got a version bump!"
author: Dachary Carey
date: 2026-04-21 22:15:29 -0400
url: /microblog/2026/04/21/the-agent-friendly-documentation-spec-just-got-a-version/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7452540552354369537
draft: false
---

The Agent-Friendly Documentation Spec just got a version bump! 🎉

Spec v0.4.0 reflects a handful of community requests, including:
- Renaming the `llms-txt-freshness` to `llms-txt-coverage` to more accurately capture intent
- Noting that `llms-txt-coverage` may intentionally diverge from full sitemap parity for docs sets that curate their coverage, and makes allowances for different coverage measurement modes
- Updating `markdown-content-parity` for a similar purpose; some sites intentionally omit info from markdown pages that aren't relevant to agents, such as chrome, buttons, or sign up CTAs

I'm planning to update the `afdocs` CLI soon to reflect these updates; hopefully over the weekend.

Major thank you to everyone who weighed in, including [@devalog](https://github.com/devalog), [@njb90](https://github.com/njb90), [@nicoleomeara-astro](https://github.com/nicoleomeara-astro), [@rosieyohannan](https://github.com/rosieyohannan), [@MattyQ](https://github.com/MattyQ), and [@chalin](https://github.com/chalin). Really grateful for everyone's feedback to help make this a more useful tool for the community! 🎉
