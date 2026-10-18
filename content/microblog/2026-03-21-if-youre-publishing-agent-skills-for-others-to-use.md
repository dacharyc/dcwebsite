---
title: "If you're publishing Agent Skills for others to use"
author: Dachary Carey
date: 2026-03-21 14:24:28 -0400
url: /microblog/2026/03/21/if-youre-publishing-agent-skills-for-others-to-use/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7441187990585929728
draft: false
---

If you're publishing Agent Skills for others to use: do you know what happens when your skill loads on a platform you haven't tested?

The Agent Skills spec defines a portable format. Write a SKILL.md once, users install it anywhere. Over 25 platforms support it. But "support" covers a wide range of actual behavior. Your skill's reference files might load automatically on one platform and be invisible on another. Your frontmatter might reach the model on one platform and get stripped on another. Your instructions might survive a long conversation on one platform and get silently pruned on another.

The spec's implementation guide says "every skills-compatible agent follows the same three-tier loading strategy." That guide was based on analysis of 7  platforms. At least 25 had shipped their implementations before it was published.

We don't actually know how most platforms handle skill loading. Nobody does. So help me find out!

I've started a community research project cataloging 22 loading behavior checks across 9 categories: loading timing, directory recognition, resource access, content presentation, lifecycle management, access control, structural edge cases, skill-to-skill invocation, and skill dependencies. The repo includes 16 benchmark skills with embedded canary phrases that reveal exactly what a platform loaded and when, without relying on model self reporting.

What we need now is people testing on real platforms. If you use Agent Skills on any platform (Claude Code, Cursor, GitHub Copilot, Roo Code, or any of the others), you can run the benchmark skills and submit your findings. Even partial results from a single platform are valuable. One empirical observation beats a hundred assumptions.

This is community science for the agent ecosystem. The findings will be published at https://agentskillimplementation.com and the full project (benchmark skills, test procedures, results template) is on GitHub: https://github.com/agent-ecosystem/agent-skill-implementation

If you're distributing official skills and care about them working across platforms, this is directly relevant to you. And if you've already noticed your skills behaving differently on different platforms, your observations are exactly what we're looking for.
