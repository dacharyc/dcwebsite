---
title: "I've been chatting with a lot of technical writers and docs orgs as a result of…"
author: Dachary Carey
date: 2026-02-26 08:38:45 -0500
url: /microblog/2026/02/26/ive-been-chatting-with-a-lot-of-technical-writers-and-docs/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7432781167801638913
draft: false
---

I've been chatting with a lot of technical writers and docs orgs as a result of the recent work I've been doing with the Agent-Friendly Docs Spec and the `afdocs` tool to validate docs against the spec.

Some folks have been concerned that this looks like a *big* project and they don't have the time or resources for it. But the reality is, it can look as small or as big as you want it to depending on how much this matters to you right now.

Some folks I've been chatting with have made one or a handful of fixes to their docs. Even just small changes can help.

If you can only do *one* thing right now, start with page size. The three checks you care about are:
- `page-size-markdown`: If the character count in markdown is too long, the content gets summarized and the agent will never see the full page. Long API references are likely to fall into this category. Break them up into smaller pages so the agent can actually see the content, instead of a summary that may miss important details.
- `page-size-html`: HTML adds more content with less semantic value for the agent, and may hide content away through JS elements. Your HTML pages are probably *much* longer than a markdown page would be and more likely to trigger summarization. Work with your platform to reduce the HTML page size, or add agent discoverability directives to point agents to the markdown version of your pages. (Or both!)
- `content-start-position`: This mainly applies to pages in HTML; depending on how the agent platform handles the HTML -> markdown conversion, some agents will *never see your page content*. Try to get your actual documentation content to start as close as possible to the beginning of the page content. In my experimentation, I found one page where the content was only ~60 lines and it started 87% of the way through the page. That's a big problem for agents.

Try out these checks for yourself using the `afdocs` CLI and lib you can install through npm. See how your docs actually perform, and check regularly as you iterate to make improvements.

And let us know what fun and interesting things you run into along the way! The platform providers aren't incentivized to study these things, or tell us about them. So we need to figure out the edges ourselves and share with each other so we can all prepare for this new type of docs consumer together.
