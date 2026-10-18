---
title: "The Agent-Friendly Documentation Spec got a version bump over the weekend…"
author: Dachary Carey
date: 2026-09-14 08:37:58 -0400
url: /microblog/2026/09/14/the-agent-friendly-documentation-spec-got-a-version-bump/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7505243446832476160
draft: false
---

The Agent-Friendly Documentation Spec got a version bump over the weekend, bringing it to 28 checks (5 new) at v0.6 😅

The new checks include:
- `bot-protection-interference`: bot-protection systems can interfere with coding agent documentation fetching, so this check identifies three different forms of bot protection that can block coding agents from viewing your docs
- `page-size-transfer`: the spec already had `page-size-html` but that was a post-conversion metric; the new check is about how much of the content is serialized framework payloads, which is another form of "request is too large so coding agents can't see your content"
- `single-fetch-completeness`: looks for pagination in markdown responses, and verifies the continuation is machine-followable
- `markdown-link-portability`: links served in markdown should be absolute, not relative, and should resolve to the representation they promise - not a soft-404
- `embedded-data-serialization`: machine-generated static representations of dynamic content can push otherwise prose-light pages beyond coding agent truncation limits, so this check is about the size of those bulk elements

I've also added two new interaction effects:
- Bot Protection Degrading Scan Reliability: bot protection enforcement can engage mid-scan, so this interaction effect is about identifying what bots can see and measuring it before bot protection engages
- Dynamic Content Rendered Statically: this involves three of the new checks, plus `markdown-content-parity` and the Category 3 size checks, and is about the different ways that flattening dynamic content for agents can fail

Notably, these additions also pushed the Agent-Friendly Documentation Spec website beyond the truncation limit for agent fetches, so I've now broken the website down into separate pages so agents can consume it. Since this breaks the old URL path, I've also moved it to a `web` URL, officially recognizing that this is the "Web Documentation Delivery Spec" - and committing to at least two more companion specifications I've been dancing around for months:
- Content composition: what documentation should contain to serve agents well: factual consistency across pages, structure and density suited to machine consumption
- Repository-local documentation: docs serving coding agents that work inside a repository, where discovery happens through grep and file listings rather than HTTP

If you have any thoughts about what needs to go in the new content composition or repository-local documentation specs, please file issues on the GitHub repo at `agent-ecosystem/agent-docs-spec` - I am happy to discuss!

The `afdocs` CLI is not yet updated to reflect spec v0.6; I'm hoping to get those changes into the CLI/lib in the next week or two.
