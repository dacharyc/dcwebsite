---
title: "For anyone who uses agent-ecosystem tools, I've had a busy weekend for…"
author: Dachary Carey
date: 2026-08-24 08:02:41 -0400
url: /microblog/2026/08/24/for-anyone-who-uses-agent-ecosystem-tools-ive-had-a-busy/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7497624421915099136
draft: false
---

For anyone who uses `agent-ecosystem` tools, I've had a busy weekend for maintenance and everything has gotten updates:

- afdocs: couple of small bug fixes and `--canonical-origin` now accepts a prefix path to support previews shipping from a different base
- skill-validator: couple of small bug fixes in YAML parsing and llm-judge instructions
- agentsummons: updated for current Antigravity, Claude Code, and Codex harness versions
- agentminutes: updated for current Antigravity, Claude Code, and Codex harness versions + added prompt token normalization (to some degree, the accounting is weird) across Claude Code + Codex. Notably, Codex's log format changed again so there are some changes to how you would promote certain event types.
- skillxp: updated `agentsummons` and `agentminutes` dependencies for current harness versions

Thanks to everyone who PR'd and filed issues on the tools. Really happy folks are finding them useful.
