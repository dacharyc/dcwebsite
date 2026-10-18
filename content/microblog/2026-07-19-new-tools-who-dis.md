---
title: "New tools, who dis?"
author: Dachary Carey
date: 2026-07-19 23:39:09 -0400
url: /microblog/2026/07/19/new-tools-who-dis/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7484814129854144512
draft: false
---

New tools, who dis?

I keep messing with cross-harness experiments, and running into the same problems over and over:

- My agent re-discovers the valid headless invocation flags in every session, and runs into behavioral quirks that only turn up in use
- I haven't found an observability tool that works with the harnesses I want to observe, doesn't mess with the sessions, and is accessible to me as an individual
- If I want to read and compare session logs to evaluate experiment results, they produce different shapes that have to be normalized for valid comparison

I can't decide if it's a fool's errand given how quickly these things change, but I've spun up two tools to help:

- agentsummons: CLI to help manage invoking agent sessions for experiments
- agentminutes: CLI to parse native agent harness session logs into one unified, comparable event schema

Built in Go, because I love Go, but I've added npm and PyPI wrappers, so you can use them from your preferred ecosystems.

Very early releases (v0.2.2 and v0.2.0 respectively) and I have no idea if it'll be feasible to keep up with the churn on harness tools, but I want them and figured other people might find them helpful, so 🤞

Currently supports Antigravity, Claude Code, and Codex, because that's what I'm experimenting with at the moment, but if you want to use these and need support for another harness, I'm open to issues/PRs.

The links are in the comments.

**Links from the comments:**

- <https://github.com/agent-ecosystem/agentsummons>
- <https://github.com/agent-ecosystem/agentminutes>
