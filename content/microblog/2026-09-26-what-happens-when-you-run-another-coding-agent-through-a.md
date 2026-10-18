---
title: "What happens when you run another coding agent through a benchmark designed to…"
author: Dachary Carey
date: 2026-09-26 15:32:54 -0400
url: /microblog/2026/09/26/what-happens-when-you-run-another-coding-agent-through-a/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7509696521479725057
draft: false
---

What happens when you run another coding agent through a benchmark designed to detect ways in which divergence from the Agent Skills specification breaks the cross-platform portability promise?

You find more broken promises 😢

I've added GitHub Copilot CLI support to `agentsummons`, `agentminutes`, `skillxp` - and finally to the Agent Skill Implementation benchmark group. I'm now up to testing with 4 coding agent harnesses, have added 5 new checks, and many of the checks now expose more different ways harnesses have implemented something, which breaks cross-platform portability in ways I hadn't observed yet.

New on the site:
- A GitHub Copilot CLI platform report, and updates to the cross-platform comparison tables
- 5 new checks
- Guidance was formerly only "For authors" - now I've broken down guidance into 4 different pages for different personas:
  - Skill authors
  - Skill installers and users
  - Skill distributors (people designing plugins, directories, marketplaces)
  - Coding agent harness implementers

Notable new details include:
- Copilot CLI is the first coding agent I've observed enumerating every file path when you activate the skill, which means every non-skill file inside your skill bundles, like `evals/` or READMEs, license files, benchmark or signing files, or other non-skill info, carries a token cost in file path enumeration for every skill activation
- Like Claude Code, Copilot CLI strips YAML frontmatter from skill content, so models never see `compatibility` or `version` fields or other details that you want to convey
- A `description` field that is over the length, which Copilot CLI counts in UTF-16 code units (a new unit I haven't observed yet in other harnesses), gets the skill rejected from the catalog entirely, making this the strictest description enforcement I've observed
- Copilot CLI rejects non-ASCII skill names before any counting, which the reference validator does not, so the reference validator may pass skill names that Copilot CLI won't load in its catalog

For the full details, check out the updates on the Agent Skill Implementation website or GitHub. It has been an illuminating addition! It's making me want to add even more coding agent harnesses to the benchmark, because I'm seeing so many ways things have diverged that break cross-platform portability...
