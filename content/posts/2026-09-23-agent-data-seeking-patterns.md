---
title: Agent Data Seeking Patterns
author: Dachary Carey
layout: post
description: In which I examine agent website traffic logs to see what they're actually reaching for.
date: 2026-09-23 07:00:00 -0500
url: /2026/09/23/agent-data-seeking-patterns/
image: /images/agent-data-seeking-patterns-hero.jpg
tags: [ai]
draft: false
---

Two conversations this month sent me back into my server logs. The first was the ongoing industry debate about whether `llms.txt` does anything, which flares up every time a well-known project [adds one or removes one](https://dacharycarey.com/2026/05/04/astro-removed-llms-txt/). The second was a colleague mentioning that answer engines and crawlers were fetching an `llms-info` file from their site, which was news to me, because I'd never seen a request for one.

Both are really the same question: what do agents actually reach for when they visit a website? Not what we've told them to reach for, not what the spec du jour says they should reach for, but what shows up in the logs when nobody is watching. I've been running an [agent traffic classifier and signal tracker](https://dacharycarey.com/2026/04/04/measure-agent-web-traffic-redux/) across my sites since April, and it had been a while since I validated the patterns baked into it. So I pulled nine days of logs (September 11 through 19) from twelve sites, re-classified everything, and went looking.

The short version: almost every "tell the agents about your site" convention in my logs is being requested exclusively by tools built to check for it. No agent reaches for them. The one exception is `llms.txt`.

## What's in nine days of logs

Some scope first, because the numbers only mean something in context. Twelve sites, about 72,600 requests, and most of the sites are about agents, agent-friendly docs, or agent tooling.

| Category | Share of requests |
|---|---|
| Real browsers: load assets, navigate within the site (humans, plus any agent driving a real browser) | 23% |
| Browser user agents that never load an asset (unclear) | 10% |
| Automation wearing browser user agents (see below) | 25% |
| Bots the curated database doesn't know (isbot fallback) | 10% |
| AI training crawlers | 7% |
| AI assistants fetching on behalf of a user | 5% |
| Feed readers, social previews, search crawlers, SEO tools | 15% |
| Self-identified coding agents | 0.6% |

The human rows need explaining, because my first pass had a single "human browsers" row at 58%, rather than the 23% it currently shows. My classifier calls anything with a browser user agent and no bot marker human, and this audit initially showed a quarter of all traffic wearing that costume without behaving like a browser. But what does that mean?

The largest piece is one distributed crawler: about 9,000 requests from nearly 1,000 IPs, mostly in Tencent Cloud address space, using a frozen set of thirty early-2024 browser user agents (Chrome 120 to 123, Edge 122, Firefox 123, Safari 17.3, an iOS 13 iPhone). It sends browser security headers to look real, but gets them wrong in ways a browser mechanically can't: the Firefox and Safari costumes send Chromium client hints, and the Chrome ones report a version that disagrees with their own user agent. Its request rate is flat around the clock, it averages two requests per IP, and almost none of those IPs ever load a stylesheet or an image. That's not a human, or at least not in the sense that traffic metrics mean it.

The second piece is generic Chrome/131 traffic from a couple hundred IPs that turns out to be vulnerability scanners and fetch frameworks sharing a user agent string. (If you ever want to feel concerned about running WordPress, check your server logs for vulnerability scanners. 😬)

Remove these two initially-misclassified-as-human traffic sources, and about a third of human-looking traffic is left. Only the part that loads assets or navigates within the site behaves like a rendering browser. That 23% is the ceiling on humans, but we can't be certain about how many of those are actually human, because an agent driving a real browser looks exactly the same in the logs.

Detectable coding agents are a rounding error in raw request counts. They only become visible through my custom signal tracker, a small shim on each site that logs full request headers when a request negotiates for markdown, fetches a `.md` URL directly, or fetches `llms.txt`. Those three triggers produced about 1,240 signal entries over the nine days, and those entries are where I found all the interesting behavior.

Related note: the audit turned up a lot of drift in my classifier since April: new self-identifying agents (GitHub Copilot's fetch runtime, Qoder, ZCode, Grok's agent, DeepSeek's harness), a batch of new AI crawlers and search bots from Moonshot, Tencent, Huawei, Alibaba, Zhipu, xAI, and Mistral, and a couple of outright bugs in my heuristics. I fixed those before drawing any conclusions, so the categories above reflect the corrected classifier. My takeaway: five months is too long to let something like this sit in the fast-paced world of AI/agents, so it needs to be re-validated more often.

## Solutions in want of problems

I've been describing a pattern I keep seeing in this space as *solutions in want of problems*: someone decides a convention is the right answer, writes a checker or a crawler or a scoring tool that looks for it, and in practice no agent ever reaches for the thing. The checker becomes the convention's entire traffic.

The logs are unambiguous about this. Here is every agent-discovery convention that was requested on any of my sites, who requested it, and whether a single request came from an agent, an assistant, an answer engine, or a training crawler.

| File | Requests | Who | From an agent or AI vendor? |
|---|---|---|---|
| `llms-info` (any spelling, any location) | 0 | nobody | no |
| `llms-full.txt` | 4 | PoweredByBot, curl | no |
| `ai.txt` | 22 | SEOJuice, PipericBot, PoweredByBot | no |
| `.well-known/agent-card.json`, `agents.json`, `agent.json`, `mcp`, `mcp/server-card.json` | 12 | AgentTrustBot, curl | no |
| `.well-known/tdmrep.json` | 5 | NVBot, a PostHog image bot | no |

Every one of these was a 404, because I don't serve any of them. Every requester is an SEO auditor, an "agent trust" directory, or a scanner driving curl from a cloud box. My favorite is AgentTrustBot, whose user agent literally says `purpose=AI Agent Discovery`, methodically probing five well-known paths for agent cards and MCP server cards on sites that have none. It is a crawler built to discover agents, discovering that there is nothing to discover, and it is the only thing that has ever asked.

`llms-info` got zero requests. Not "few." Zero, across twelve sites and 72,600 lines. A request for a path that doesn't exist still produces a 404 line in the access log, so this is a true absence rather than a filtering artifact. When my colleague sees traffic to that file, they are almost certainly seeing traffic they induced: they linked it from `llms.txt` or `robots.txt` or a sitemap or a blog post and a crawler followed the link, or a tool they run checks for it, or someone on their team pointed an agent at it. The referrer and user agent on those requests claims that most of the traffic is OpenAI-related, but I can't reproduce that in my data. What I can say is that nothing on the open web is independently probing for it, and given that nothing probes unprompted for `llms.txt` either, apart from SEO tools, I'd be surprised if a newer convention were different.

## llms.txt: same origin, different outcome

Now the file everyone argues about. `llms.txt` got 198 requests over the nine days, 0.27% of traffic. The breakdown looks exactly like the discovery files above, right up until the last row.

| Requester | Requests | Read the linked pages afterward? |
|---|---|---|
| My own tooling and testing | 61 | by design |
| The browser-spoofing crawler above (impossible header combinations) | 34 | no |
| In-page fetches from visitors' browsers (an extension or an in-browser agent) | 12 | no |
| Plausibly humans in a browser | 2 to 5 | no |
| SEO and site-audit bots | 37 | never |
| Two scanners (one rotating browser user agents, one on a cloud box probing well-known paths) | 19 | no |
| AI training crawlers (Amazonbot, Bytespider, ClaudeBot, GPTBot, SSI) | 14 | never |
| A university research crawler | 6 | one page each |
| AI search (Exa) | 2 | no |
| AI assistants and answer engines | 0 | n/a |
| Coding agents (GitHub Copilot, Claude Code, including Claude Code shelling out to curl) | 10 | five of six sessions |

The intended audience never showed up. `llms.txt` was [proposed](https://llmstxt.org) for LLM inference and training, and the fetchers that serve that purpose ignored it completely. Every user-facing assistant and answer engine fetched thousands of pages for people this week and requested `llms.txt` zero times: Amazon Quick 0 of 3,223 requests, OAI-SearchBot 0 of 632, ChatGPT-User 0 of 404, PerplexityBot 0 of 403, Claude-User 0 of 141, DuckAssistBot 0 of 116. Meta's, Google's, Mistral's, and xAI's fetchers all had the same zero. The training crawlers hit it at about the rate they hit any other URL (ClaudeBot: once in 1,319 requests) and did nothing with it afterward. Whatever the spec intended, that audience isn't reading.

In my initial data spelunking, it looked like 46 human readers had visited the file, but 34 of the 46 turned out to be the spoofing crawler, sending client hints that contradict their own user agent. So the file's largest reader by volume is a scraper pretending to be people, and it is specifically collecting `llms.txt` and `.md` URLs while it does so. Whoever runs it wants the agent-friendly version of the content and would rather not be seen taking it.

The dozen in-page fetches are something running inside real visitors' browsers that requests `/llms.txt` from the page they're on. My sites don't do that, so it's an extension or an agent working in the user's browser; two of those visitors had full sessions with assets and page-to-page navigation, and I can't tell which it was.

The audience that did show up was not on anyone's roadmap. Two coding agents fetched `llms.txt` this week, across six sessions, and in five of them the agent then fetched the pages it linked to.

The Claude Code session is the one I'd frame and hang on the wall. It landed on `afdocs.dev` looking for CLI documentation. It guessed `/cli` and got a 404. It guessed `/docs/cli-reference` and got a 404. Then it fetched the homepage, fetched `llms.txt`, and fetched exactly two of the pages listed there: `/reference/cli.md` and `/run-locally.md`. That is wayfinding. The agent tried to navigate from memory, failed, found the map, and used it. It's also worth noting that the homepage it fell back to includes the in-page directive pointing at `llms.txt` that my [spec](https://agentdocsspec.com) recommends, which is consistent with what I argued in the Astro piece: agents don't know to fetch this file unless something on the page tells them about it.

GitHub Copilot's fetch runtime did the same thing in three separate sessions from one developer, on the same site. Each time: fetch `llms.txt`, then pull 19 to 26 of the linked markdown files. That's not a curious poke at the file; that's an agent treating it as a table of contents and reading the book.

The other two sessions were also Claude Code, but they didn't show up as Claude Code. In both, the agent had been reading the site through its fetch tool, which sends a self-identifying user agent, and then dropped to the shell and ran curl. One had just fetched four spec pages the normal way, then fifteen seconds later curl'd `llms.txt` and three of the markdown files it lists. The other had guessed a URL through the fetch tool, gotten a 404, and one second later curl'd the same wrong URL, then a second guess, then `sitemap.xml`. I only know these were Claude Code because the same IP sent the real user agent moments earlier. On their own, the curl requests are indistinguishable from a script, and curl's default `Accept: */*` never trips the content-negotiation signal that triggers my header-capture PHP shim, so a shell-driven agent is invisible to the tracker unless it reaches for `llms.txt` or a `.md` URL by name. Which these did. That's its own data-seeking pattern: when the agent is holding a shell instead of a fetch tool, it asks for markdown by URL rather than by header.

Given the sample size, this evidence reduces to anecdata. It's six sessions from four developers in nine days, on two sites. What the data supports is "coding agents use `llms.txt` when they find it, and nothing else uses it at all." It does not support "coding agents seek it out." Cursor, for instance, only fetched `llms.txt` when I asked it to in a controlled test; left to explore a site on its own, it went for `sitemap.xml` instead. But the asymmetry is the whole point. The file was designed for one audience that ignores it and turned out to be exactly the right shape for a different audience that had no other map. It's an accident. It's a useful accident, because thousands of sites already publish one.

In the Astro post I said the right question for a docs team isn't "how many page views does `llms.txt` get" but "how many agent sessions touched it, and what happened next." I can now answer that for my own sites: a handful, and all but one of them followed the links. That's a small number but a very clear signal.

## What agents reach for without being told

The contrast that makes the argument is what agents do when nobody has pointed them at anything.

Content negotiation for markdown, where the agent sends `Accept: text/markdown` on an ordinary page URL, produced over 500 signal entries in nine days. Claude Code does it on every single page it fetches. Cursor does it on every fetch, through a rotating pool of proxy IPs. Copilot's runtime does it. So do the newer entrants: Grok's agent, ZCode, Qoder. Direct fetches of `.md` URLs produced another 465 entries, and that's an undercount because the tracker rate-limits that trigger per IP.

Those two behaviors work on any page, on any site, with no metadata, no discovery file, and no well-known path. And they outnumber every discovery-file request in the logs combined by roughly five to one.

So the shape of agent data-seeking, at least on my sites this month, is: fetch the content, ask for it in a format you can read, and if you need a map, take one that's already lying around. What agents do not do is look for a file that announces the site is ready for them. The sites that build those files get visits from the tools that grade sites on having them; I can't see any evidence of agents actually using them.

## What I'd take from this

If you're a docs team deciding what to invest in for agents, the logs suggest a fairly short list.

**Serve markdown, and honor content negotiation.** This is the thing agents do unprompted, constantly, baked into many harnesses I've been able to identify as specific agents. A site that returns clean markdown for `Accept: text/markdown` or at a `.md` URL is serving the actual behavior, not a hypothesized one.

**Keep your `llms.txt`, and point to it from your pages.** It costs almost nothing, coding agents use it as a navigation index when they find it, and the in-page directive is what gets them there. Don't expect answer engines or training crawlers to care; they don't, and the data says they never did.

**Be skeptical of the next discovery file.** `llms-info`, `ai.txt`, agent cards, MCP server cards, TDM reservation files: none of them has a single agent or vendor request in my logs. If someone tells you they're seeing traffic to one, ask to see the user agents and referrers before you build anything. There's a decent chance it's a checker checking, or a link they published being followed.

**Measure sessions, not page views.** The number that told me something this month wasn't how many times `llms.txt` was fetched. It was how many times a fetch was followed by requests for the pages inside it, and by whom. That's harder to extract, but it's the question you actually want answered.

I'll keep running this. The classifier and the audit scripts are [open source](https://github.com/agent-ecosystem/agent-traffic-classifier) if you want to run the same analysis on your own logs. I'd be really curious to hear whether other people's sites tell the same story or a different one.
