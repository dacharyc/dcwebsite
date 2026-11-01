---
title: "Another weekend, another big round of updating the various libs and tools"
author: Dachary Carey
date: 2026-11-01 12:12:43 -0500
url: /microblog/2026/11/01/another-weekend-another-big-round-of-updating-the-various-libs-and-tools/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7522707205914079232
draft: false
---

Another weekend, another big round of updating the various libs and tools I maintain:

- `afdocs`: v0.23.0 fixes some locale-handling bugs and adds support for script-subtag locale codes
- `agentsummons`: v0.4.1 updates for current harness versions, including some new flags in Antigravity CLI and GitHub Copilot CLI
- `agentminutes`: v0.9.0 updates for current harness versions, including changes to Claude Code API shape, support for a Codex compressed rollouts, and renamed Antigravity CLI tools + new usage information available
- `skillxp`: v0.4.1 updates for current harness versions and revalidates skill loading lore
- `agent-traffic-classifier`: v0.7.0 includes some more triggers for identifying something as a spoofed browser, which should register as bot activity, not human activity. Also more probe patterns for filtering, and more entries to the bot and scanner lists.

I've also updated Agent Skill Implementation for the current versions of the harnesses, and found for the first time since starting to run automated check in July that two of the harnesses have made harness changes that affect skill behavior. I've added a changelog to the site here: [Changelog](https://agentskillimplementation.com/changelog/)

So not only does skill behavior vary by harness, but *also* by harness version. Something to be aware of and make sure you're testing for when you distribute skills. (And this is independent of model behavior, which also affects skill behavior in some instances - the changelog distinguishes model behavior from platform behavior.)
