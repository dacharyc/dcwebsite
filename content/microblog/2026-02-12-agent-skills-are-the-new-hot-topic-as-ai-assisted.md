---
title: "Agent Skills are the new hot topic as AI-assisted engineering practices shift…"
author: Dachary Carey
date: 2026-02-12 19:02:12 -0500
url: /microblog/2026/02/12/agent-skills-are-the-new-hot-topic-as-ai-assisted/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7427864632553558016
draft: false
---

Agent Skills are the new hot topic as AI-assisted engineering practices shift from prompt-focused to context-management.

To me, that means content governance and the content management lifecycle are back in vogue.

Nobody with experience managing content wants to maintain a duplicate source of truth. PostHog shared an article recently about an AI Wizard they created, and one of the problems they cited was drift between the maintained source of truth - docs and the product - and the AI Wizard content. Now picture that at scale across a bunch of different Agent Skills, most of which are duplicating content that already lives in your docs.

Some folks are solving for this by pointing LLMs at docs and saying “make this a Skill!”

Don’t do that.

Probabilistically copying your docs *through* an LLM is a recipe for omissions and conflation. And when you need to do it again in a month or 6 months when the product changes, you will get completely different outputs. It’s probabilistic.

Instead, beg, borrow, steal, or vibe-code your own system to deterministically create Skills from your docs source. That way, you can regenerate it as often as you need to as your product evolves, without maintaining a second source of truth or relying on a probabilistic system that will create unsustainable review and maintenance overhead.
