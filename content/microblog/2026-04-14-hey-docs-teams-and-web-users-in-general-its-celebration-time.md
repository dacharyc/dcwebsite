---
title: "Hey, docs teams (and web users in general) - it's celebration time!"
author: Dachary Carey
date: 2026-04-14 19:34:08 -0400
url: /microblog/2026/04/14/hey-docs-teams-and-web-users-in-general-its-celebration-time/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7449963233840451584
draft: false
---

Hey, docs teams (and web users in general) - it's celebration time! Claude Code has shipped an update that fixes the configuration of its HTML to markdown lib and now it can use gigantor docs pages buried under a mountain of CSS more effectively 🎉

In the v2.1.105 release notes, Claude Code has this unassuming bullet item:

```
- Improved `WebFetch` to strip `<​style>` and `<​script>` contents from fetched pages so CSS-heavy pages no longer exhaust the content budget before reaching actual text
```

An engineer who reverse-engineered Claude Code's WebFetch implementation pointed out in January that it uses the Turndown library to convert HTML to markdown. But as I found in an article I posted in February, Claude Code's Turndown implementation was not configured to strip `<​style>` or `<​script>` tags, so web pages with a lot of CSS or JS script content would eat the entire context window before ever reaching any real page content.

With this fix, Claude Code can now successfully "see" content in large HTML pages! I've confirmed this with some known "bad" pages that Claude consistently complains about. Complaints are gone - Claude can now successfully retrieve the content!

This is good news for documentation teams who do not yet serve clean markdown through content negotiation. Now Claude Code stands a much better chance of being able to find your content in HTML. 🎉
