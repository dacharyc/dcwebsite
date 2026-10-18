---
title: "Just pushed another batch of updates to the afdocs tool that enables you to…"
author: Dachary Carey
date: 2026-02-24 09:08:31 -0500
url: /microblog/2026/02/24/just-pushed-another-batch-of-updates-to-the-afdocs-tool/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7432063886012346368
draft: false
---

Just pushed another batch of updates to the `afdocs` tool that enables you to test your documentation site against the Agent-Friendly Documentation Spec. The v0.3.0 update adds the remaining 4 full-automated checks with clear pass/warn/fail conditions, bringing it up to 14 of 21 checks implemented. The new checks are:

- `markdown-code-fence-validity`: if you have unclosed code fences, agents will interpret any content after the opening code fence as code, leaving the content subject to misinterpretation
- `http-status-codes`: whether error pages return appropriate status codes, or "soft" 200 on 404s that may confuse agents
- `cache-header-hygiene`: can agents see fresh data, or are you setting long caches that mean agents may get outdated information?
- `auth-gate-detection`: do documentation pages require authentication to access content? If so, can agents figure that out, or is the type of return response ambiguous

The remaining checks are more subjective and open to different interpretations. I'll implement heuristics that make sense to me given the behavior I've observed, but your organization might care about or want to measure those things differently. Feel free to comment on the Agent-Friendly Spec repo to discuss what implementation might look like to you, so we can consider codifying more details in the spec.

Package on npm: https://www.npmjs.com/package/afdocs

**Links from the comments:**

- <https://github.com/agent-ecosystem/agent-docs-spec>
