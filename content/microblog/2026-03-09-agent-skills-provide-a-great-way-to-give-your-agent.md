---
title: "Agent Skills provide a great way to give your agent reusable workflows…"
author: Dachary Carey
date: 2026-03-09 07:29:54 -0400
url: /microblog/2026/03/09/agent-skills-provide-a-great-way-to-give-your-agent/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7436735009501114368
draft: false
---

Agent Skills provide a great way to give your agent reusable workflows, information about up-to-date product details, or custom domain knowledge. But my audit of 673 agent skills last month found that 22% fail basic structural validation, and 52% of tokens across these skills were in non-standard files potentially wasting your agent's context.

In short, there's a real quality gap in published agent skills. And using skills that don't meet minimum quality standards can actually degrade your agent experience. So I wrote skill-validator to help people assess their skills.

As I've been socializing skill-validator with people creating skills, I realized some skill authors may need additional help interpreting the output or figuring out what changes to make to improve their skills. So I've added some examples of tools I've created to help authors ensure their skills meet a minimum quality bar to the skill-validator repo:

- A `review-skill` skill that instructs an agent how to run skill-validator, interpret the results, and provide actionable feedback to skill authors
- A CI workflow that runs structural validation checks on PRs with new or changed skills, and fails CI for skills that don't meet structural quality requirements, potentially blocking merge

Whether you're adding your own or someone else's skills to a project, or publishing official company skills for customers to use, these tools can help you ensure the skills you're consuming or producing meet minimum quality requirements.

**Links from the comments:**

- <https://github.com/agent-ecosystem/skill-validator/tree/main/examples>
