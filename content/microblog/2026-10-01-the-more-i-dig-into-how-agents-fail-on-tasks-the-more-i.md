---
title: "The more I dig into how agents fail on tasks, the more I find about how product…"
author: Dachary Carey
date: 2026-10-01 08:43:22 -0400
url: /microblog/2026/10/01/the-more-i-dig-into-how-agents-fail-on-tasks-the-more-i/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7511405397119799297
draft: false
---

The more I dig into how agents fail on tasks, the more I find about how product surfaces need to change to reflect this new consumer. Let me tell you about two recent examples:

First, version information in docs. I was able to reproduce agent failures consistently related to missing version information, on multiple axes. When humans go to a versioned documentation page, they typically see version information on the page. Maybe it's interpolated into the page title or header, and usually there is a version switcher that lets humans select a different version of the docs.

Agents don't see that. When agents hit the markdown versions of the page, like Claude Code requests markdown by default through content negotiation, if the markdown content doesn't include the version information, Claude doesn't see it. And most sites I've seen are not generating version information into the markdown. Here's what this failure looks like in practice:

- Claude sees an install page that shows how to pin a version, which includes v0.5.1. Claude installs v0.5.1. But the product has moved on to v0.6.0. Except Claude doesn't see anything about v0.6.0 on the page.
- Claude looks up documentation for specific functionality. That documentation reflects functionality in v0.6.0, but it doesn't say so on the page. Claude tries to use it, but the functionality doesn't exist in the version of the product it's using, which is behind.
- This is where I've seen it diverge: Claude either tries to build its own version of the feature as written without mentioning anything to the user, or tells the user that the functionality is absent and it can't do the requested task, or implements something completely different which it may or may not call out to the user. I have never yet seen the agent recognize that the version is behind, or even check GitHub to figure out the discrepancy, even when the product is public.

So missing version information bites agents in multiple ways: it can cause the agent to install old or outdated products even when looking at up-to-date documentation, or it can cause the agent to reach for functionality that doesn't match the installed version of the software.

A second example is related to patterns that exist in training data, and how agents can confidently invent things that don't exist. I was working with a product that has a `start` and a `status` command. Claude retrieved documentation for the product, which uses its web fetch tool with a smaller model, Haiku, summarizing page content for the orchestrator model - in my case, Opus 5.5. Haiku tells Opus that the product has a `start`, `status`, and `stop` command.

The product doesn't have a `stop` command. It uses `ctrl + c` to stop. But Haiku is a small model, and there is enough `start`, `status`, `stop` patterns in its training data that it makes up the plausible command that doesn't exist. So my open question is - should the product *add* a `stop` command?  🤔
