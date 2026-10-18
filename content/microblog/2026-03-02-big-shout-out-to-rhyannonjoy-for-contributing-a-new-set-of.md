---
title: "Big shout out to @rhyannonjoy for contributing a new set of known platform…"
author: Dachary Carey
date: 2026-03-02 23:58:47 -0500
url: /microblog/2026/03/02/big-shout-out-to-rhyannonjoy-for-contributing-a-new-set-of/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7434462255204868096
draft: false
---

Big shout out to [@rhyannonjoy](https://github.com/rhyannonjoy) for contributing a new set of known platform truncation limitations to the Agent Friendly Docs Spec!

Rhyannon used some Python-foo and the same data I used in my Agent Web Fetch Spelunking article to test the limits of the Claude API with the `web_fetch` tool enabled - similar to, but not the same as, the Claude Code web fetch implementation.

I'm sorry to say the results do not bode well for the average documentation page, friends. 😰 My initial findings with the Claude Code web fetch + summarization model were much more generous than the Claude API's web fetch default truncation limit.

What this means is that anyone who's *built* something that uses the Claude API/Claude platform - including possibly repackaging Claude in other agent tooling - may have far more aggressive truncation limits. Which means even less of our docs content may be making it through than I initially thought based on Claude Code.

Help us do science to it! Check out Rhyannon's PR, which I'll link in the comments, for an idea of how we can approach these problem domains.

Pick something you're interested in learning more about, do some testing, and share the results! We will all benefit.

(And if you contribute back to the Agent-Friendly Docs Spec with your findings, we can keep building out the constraints so we can adapt our docs systems to serve them.)

**Links from the comments:**

- <https://github.com/agent-ecosystem/agent-docs-spec/pull/2>
