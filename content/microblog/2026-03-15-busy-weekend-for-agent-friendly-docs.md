---
title: "Busy weekend for Agent-Friendly Docs!"
author: Dachary Carey
date: 2026-03-15 22:04:54 -0400
url: /microblog/2026/03/15/busy-weekend-for-agent-friendly-docs/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7439129540016275456
draft: false
---

Busy weekend for Agent-Friendly Docs!

- I added a new check to the spec, `rendering-strategy`, which checks whether a docs site renders content that is visible to tools like Claude Code and Cursor that use fetch library implementations instead of embedded or headless browsers
- I updated spec pass/warn/fail details based on practical details discovered during implementation
- I completed implementing the remaining checks in the `afdocs` CLI and library available through npm 🎉  All 22 checks are now fully implemented.

If you have Node.js/npm installed, you can run the tool locally to check your docs site for agent-friendliness:

`npx afdocs check https://docs.example.com`

The tool is now at v0.5.0; I'm going to leave it a v0.x tool for a bit longer while I wait to hear any bug reports from folks who are trying it out. Depending on how much feedback I get, and my own experience using it, I'm hoping to cut a 1.x release in April.

If you try it out, please do file any issues with bug reports or request enhancements on the tool repo. I'll throw links in the comments.

**Links from the comments:**

- <https://github.com/agent-ecosystem/afdocs>
- <https://www.npmjs.com/package/afdocs>
- <https://agentdocsspec.com>
- <https://github.com/agent-ecosystem/agent-docs-spec>
