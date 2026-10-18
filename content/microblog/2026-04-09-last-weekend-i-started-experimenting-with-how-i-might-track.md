---
title: "Last weekend, I started experimenting with how I might track agent website traffic"
author: Dachary Carey
date: 2026-04-09 08:44:37 -0400
url: /microblog/2026/04/09/last-weekend-i-started-experimenting-with-how-i-might-track/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:ugcPost:7447987838601711616
images:
  - src: /images/microblog/2026-04-09-last-weekend-i-started-experimenting-with-how-i-might-track-1.jpeg
    alt: "Horizontal stacked bar chart titled Agent Activity, counting requests per coding agent and split into content negotiation, llms.txt, and identified-by-user-agent segments. Claude Code (Anthropic) leads at about 2,150 requests, followed by Cursor (suspected, Anysphere) at about 1,350, markdown agent at about 800, unidentified at about 775, markdown agent (minimal) at about 675, unidentified AI assistant at about 525, axios-pattern agent at about 225, then Gemini CLI, MCP Client, Cursor, Kiro, GitHub Copilot, and markdown.new with under 150 each."
  - src: /images/microblog/2026-04-09-last-weekend-i-started-experimenting-with-how-i-might-track-2.jpeg
    alt: "Horizontal bar chart titled AI Bot Activity, counting requests from AI crawlers and answer engines. ChatGPT-User (OpenAI) leads at about 585, then GoogleOther at about 285, Amazonbot at about 275, GPTBot at about 190, GoogleAgent-URLContext at about 175, JarvisSearch at about 145, ClaudeBot at about 115, Meta-ExternalAgent at about 80, OAI-SearchBot at about 60, Kagibot at about 60, PerplexityBot at about 45, Bytespider at about 15, and CCBot (Common Crawl) near zero."
  - src: /images/microblog/2026-04-09-last-weekend-i-started-experimenting-with-how-i-might-track-3.jpeg
    alt: "Horizontal bar chart titled Programmatic Access, counting requests by HTTP client library with the number of sites each hit. curl leads at about 935 across 3 sites, then node at about 625 (7 sites), urllib at about 575 (agentreadingtest.com only), trafilatura at about 545, newspaper at about 470, python-requests at about 465 (5 sites), httpx at about 425, aiohttp at about 380, Go-http-client at about 240 (all sites), undici at about 205, axios at about 195, and http.rb, node-fetch, okhttp, Ruby, Java, colly, and Wget under 80 each."
draft: false
---

Last weekend, I started experimenting with how I might track agent website traffic. This week, someone shared my agentreadingtest.com website to Hacker News. The timing was amazing - I had already set up the site to track agent signals, and suddenly I had thousands of Hacker News site visitors pointing their agents at my site.

What a rich data mine!

I've been spelunking in the logs to try to add more agents to my tracking based on all the new data and activity I'm seeing, and I've extracted the classification logic from my metrics pipeline into a new baby library for reuse across multiple projects where I want to track this type of activity.

If you have access to log entries - either from access logs, or CDN logs - you can use this library to try to classify agent activity: `agent-traffic-classifier` - it's on npm and GitHub.

If you have access to the full header set, you can optionally construct "agent signal entries" - which the lib attempts to correlate with access logs to try to identify likely agent activity using stronger signal. I've also started adding in IP intelligence to help with things like narrowing down "unknown AI assistant" to, for example, Chinese assistants like Kimi or DeepSeek.

The lib is just a classifier - you'll need to do something with the data you get back. I'm just using Chart.js to build some simple charts for my needs, but you can do whatever you want with the data based on your needs.

This is a very very early brand new lib that is in no way ready to make stability guarantees or be used in production. But I built it because the companies that claim to track agent activity are not transparent in how they're doing that, so I wanted to explore how *I* might do that and what's actually possible. By running my own numbers, I can assess the likely accuracy of vendor solutions. If this might be helpful to you, too, feel free to play around with it. But I'm many projects deep at this point so I'm not sure this will get the same level of activity/speed of development as my other projects.

**Links from the comments:**

- <https://github.com/agent-ecosystem/agent-traffic-classifier>
- <https://www.npmjs.com/package/agent-traffic-classifier>
