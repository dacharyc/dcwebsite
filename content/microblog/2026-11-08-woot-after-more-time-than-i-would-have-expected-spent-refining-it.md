---
title: "Woot! After more time than I would have expected spent refining it"
author: Dachary Carey
date: 2026-11-08 22:55:28 -0500
url: /microblog/2026/11/08/woot-after-more-time-than-i-would-have-expected-spent-refining-it/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7525405675888218112
images:
  - src: /images/microblog/2026-11-08-woot-after-more-time-than-i-would-have-expected-spent-refining-it-1.png
    alt: "Screenshot of the Issuenator dashboard showing issues across several different repositories: `afdocs`, `agent-docs-spec`, `skill-validator`, `tealixir-website`, `PR Focus`. The selected issue rendered in the detail view is from 4 days ago and shows rendered text with headings, a code block, and some comments. There is an \"All Repositories\" disclosure group in the sidebar that is expanded, and it shows *many* of my public repositories that I'm watching for issues."
  - src: /images/microblog/2026-11-08-woot-after-more-time-than-i-would-have-expected-spent-refining-it-2.png
    alt: "Screenshot of the \"Agent Actions\" modal showing a stack of actions \"Awaiting your review\". The awaiting review actions are adding repositories with the explanation: \"You asked to add the public agent-ecosystem repositories not already in Issuenator\", which is the reason my preferred coding agent, Claude Code, used when adding multiple repositories to the app based on my natural language request. You can also see a \"History\" that includes a \"Removed repository\" request, a reverted request, and a declined request. There are buttons to accept the \"awaiting review\" actions, \"Revert All Unaccepted,\" or export a CSV if I want to keep records of actions that my agent has taken."
draft: false
---

Woot! After more time than I would have expected spent refining it, I've revived my not-touched-in-months TestFlight beta app, [Issuenator](https://issuenator.app), and added [an MCP server](https://issuenator.app/docs/agent-server/).

Now I can do things like fire up Claude Code and say "add my public repos that I'm not already watching to Issuenator" and Claude can hunt them down and add them. Or I can ask Claude to summarize new activity, apply private local-only tags, create issues, or help me decide what to work on next based on the issues folks have filed across all of my repos.

No more weekly check of all my open source repos, or forgetting to check and leaving issues sitting for weeks (or months 😅). 

Gonna live with this for a bit and then I swear this one is going to get released to sit alongside PR Focus, instead of dying in a TestFlight beta that I never actually release.
