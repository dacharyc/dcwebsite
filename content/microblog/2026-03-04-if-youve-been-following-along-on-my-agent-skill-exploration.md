---
title: "If you've been following along on my Agent Skill exploration, you've probably…"
author: Dachary Carey
date: 2026-03-04 07:28:25 -0500
url: /microblog/2026/03/04/if-youve-been-following-along-on-my-agent-skill-exploration/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7434937796399742977
draft: false
---

If you've been following along on my Agent Skill exploration, you've probably seen me post about my `skill-validator` tool. I wrote it to help with my Agent Skill Report, and I've been quietly polishing away at it alongside all the other agent-related stuff I've been doing.

Last night, `skill-validator` got a new home and a v1.0.0 release! 🎉

The `skill-validator` tool now:
- Has separated core logic into library functionality to support reuse in other contexts, like test suites and other tools
- Retains the CLI entrypoint, but at a new location
- Lives alongside other tooling and resources I'm building related to agent things, in the GitHub `agent-ecosystem` org

If you've been using `skill-validator` via brew, you'll need to tap the new homebrew tap to get 1.0.0 and future updates:

`brew tap agent-ecosystem/tap
brew install skill-validator`

I've got a lot more plans in mind for this tool, so if validating skill spec compliance + getting some quality checks is something you're interested in, keep an eye out for future updates. 😁

**Links from the comments:**

- <https://github.com/agent-ecosystem/skill-validator>
