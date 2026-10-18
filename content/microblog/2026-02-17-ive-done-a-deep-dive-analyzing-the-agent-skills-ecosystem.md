---
title: "I've done a deep dive analyzing the Agent Skills ecosystem around modular…"
author: Dachary Carey
date: 2026-02-17 09:56:02 -0500
url: /microblog/2026/02/17/ive-done-a-deep-dive-analyzing-the-agent-skills-ecosystem/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7429539127571804160
draft: false
---

I've done a deep dive analyzing the Agent Skills ecosystem around modular instruction packages that tools like Claude Code, GitHub Copilot, Cursor, and 27+ other agent platforms use. These packages are intended to extend what coding agents can do with custom instructions covering specific domain expertise.

I looked at 673 skills from 41 source repositories. Some things I found:

 - 22% of published skills fail structural validation — including skills from companies like Microsoft and Stripe
 - Over half of all tokens contained in Skills are nonstandard files (LICENSE texts, XML schemas, benchmark data) that provide little or zero value to the agent
 - Most skills just restate what the LLM already knows. My analysis indicates that novelty is the weakest quality dimension across the ecosystem, and the most important one. Novelty is information the model can't derive from training data.
 - 66 skills look clean on the surface but carry hidden potential for contamination in their reference files

I also ran a behavioral eval to see whether structural risk scores actually predict degradation in agent output. They don't (r = 0.077). The real interference comes from content-specific mechanisms: template syntax bleeding into output, framework-specific patterns leaking into unrelated tasks, and API hallucination where the model invents plausible but nonexistent SDK methods.

I published an interactive report where you can explore every skill individually; validation status, quality scores, contamination risk, token breakdown; along with the full paper:

https://agentskillreport.com/

If you're writing or using Agent Skills, the practical takeaways:
 - Run skill-validator before publishing
 - Focus on what the LLM doesn't already know
 - Clean nonstandard files out of your skill directory
 - Scope skills to a single language SDK when possible
