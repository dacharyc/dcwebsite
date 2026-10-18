---
title: "Another shout-out to @rhyannonjoy for contributing more known agent platform…"
author: Dachary Carey
date: 2026-03-10 06:41:52 -0400
url: /microblog/2026/03/10/another-shout-out-to-rhyannonjoy-for-contributing-more/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7437085309886066689
draft: false
---

Another shout-out to [@rhyannonjoy](https://github.com/rhyannonjoy) for contributing more known agent platform limits to the agent docs spec! Rhyannon has been systematically testing some of the other tools, and with their recent spelunking into Google Gemini, the picture has gotten even murkier for agent docs access.

Gemini works differently than some of the other agent platforms, and Rhyannon's observation here has implications for understanding how Gemini "sees" our docs:

> There’s no direct equivalent to the Claude API web fetch track’s character-level truncation measurement. The API doesn’t expose the text injection or where truncation occurred.

The takeaway is that with the way the Gemini API works, there isn't a way for us to get clear visibility from the API side into what the agent retrieves, and where or if a truncation boundary may exist. It's not as simple or clear as knowing Claude Code or the Claude API truncates at X characters.

It's probably possible to do some observed testing; give Gemini URLs for pages with different lengths, and ask about content on different parts of the page to determine what Gemini "sees."

But the fact that there isn't a straightforward way to discover this information underscores the challenges we face as documentarians - what is true for one platform may not be true for others, so the question of "how well can agents use our docs" has a qualifier: "which agent?" For docs teams, the question in the short term may be "which agent platforms are most of our customers using?" and trying to optimize for *that*, instead of trying to generalize rules that work for all agents.

Anyway, big shout out to Rhyannon for continuing to explore this and sharing their findings back to the Agent-Friendly Docs Spec so we can all benefit! 🎉

I'm also really enjoying the write-ups of their research - I'll throw a link in the comments if you're curious about this stuff the way I am. 😁

**Links from the comments:**

- <https://rhyannonjoy.github.io/agent-ecosystem-testing/>
