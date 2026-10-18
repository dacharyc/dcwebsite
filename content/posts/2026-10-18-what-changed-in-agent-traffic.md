---
title: What Changed in Agent Traffic?
author: Dachary Carey
layout: post
description: In which I explore changes in agent traffic from April to October.
date: 2026-10-18 12:00:00 -0500
url: /2026/10/18/what-changed-in-agent-traffic/
image: /images/what-changed-in-agent-traffic-hero.jpg
tags: [ai]
draft: false
---

When I updated my agent traffic classifier last month, I wrote about some observations related to agent data seeking patterns. And one of my takeaways was that five months was too long to let a traffic classifier sit in the fast-paced world of AI development, so today I did another check in to see how things are going. In the month since my last check, I've observed more changes, so let's talk about what's moving in agent traffic and what you need to care about if you're watching this.

## New agents

Since I started tracking agent traffic in April, there have been *so many* new agents released. It's practically a full-time job just keeping up with new agents.

In April, my classifier's curated bot database knew about seven coding agents:

- GitHub Copilot's VS Code browser
- Gemini CLI
- Claude Code
- Kiro
- Claude Agent
- MCP clients
- One oddball

Today it has 23 agent patterns, and the overall database has grown from 87 curated bots to 291. Most of that growth is not coding agents; it's the long tail of AI search engines, AI crawlers, and small independent search indexes that turned up in the logs (6 AI search bots in April, 21 now; 16 AI crawlers, 36 now).

Here's what showed up in the `agent` category across my twelve sites in one week (October 11 to 18):

| Agent | How it identifies itself | Notes |
|---|---|---|
| Claude Code | `Claude-User (claude-code/2.1.x; ...)` | 21 distinct builds in one week, 2.1.267 through 2.1.302. By far the most active agent: 251 of 407 agent signal entries. |
| Claude Desktop | `Claude/2.31226.1` token in an Electron Chrome UA | The desktop app's embedded browser, fetching pages with assets like a real browser. |
| GitHub Copilot | `Code/1.142.0` in VS Code's Electron UA, or `GitHubCopilotRuntime-WebFetch` | Two different fetch paths: the editor's embedded browser and a server-side runtime that negotiates for markdown. |
| Kiro | `Kiro-CLI` and, new this month, `KiroServer/1.0` | Amazon's agent now has a server-side fetcher too. |
| OpenCode | `Mozilla/5.0 AppleWebKit/537.36 (KHTML, like Gecko); compatible; OpenCode-User/1.0; +https://opencode.ai` | New since August. More on this below, because it solved a six-month mystery. |
| Antigravity | `Google-Jetski-Antigravity` | Google's agent. Not in the week's logs at all; I only found it by pointing it at my own site this afternoon. More below. |
| ZCode | `ZCode-WebFetch/0.1 (+https://zcode.ai; coding-agent-cli)` | Also sends an `X-Zcode-Trace-Id` header. |
| Qoder | `qodercli/1.1.66 (+https://qoder.com)` and `qoderclicn/1.1.59 (+https://qoder.cn)` | Alibaba's agent, with separate international and China builds. |
| WorkBuddy | `WorkBuddy/5.8.3` in an Electron Chrome UA | Eight different builds in one week, all fetching assets like a browser. |
| Augment Code | `Augment-WebFetch/1.0` | First sighting. |
| agent-browser | `agent-browser/0.34.0 read` | Vercel's headless browser CLI for agents. |

Agents I've added to the database since April but that didn't visit this particular week: grok-agent, deepseek-harness, Kimi Desktop, CodeBuddy, and Gemini CLI.

The assistant side grew just as fast. ChatGPT-User made 313 requests from 237 IPs this week. Amazon Quick, which I'd never seen in April, showed up as sixteen different `amazon-Quick-on-behalf-of-<id>` user agents. And there's a new one from Exa, `ExaUser`, which turns out to matter a lot.

## More agents are now sending User Agent strings

When I ran my first reports in April, practically no agents were sending User Agent strings. I was trying to resort to clever fallbacks to try to predict agents from behavior, and using local testing to identify agents by pointing them at servers I controlled. Now, lots of agents are self-identifying through user agent strings.

In March, Claude Code fetched my pages as `axios/1.8.4` and Cursor as `axios/1.13.5`. By April, Claude Code had switched to `Claude-User (claude-code/2.1.92; ...)`, and I celebrated. This week, of the 407 signal-tracker entries my classifier attributes to agents, 264 (65%) came from a user agent that names the product. The rest were identified from behavior: Accept headers, tracing headers, missing browser headers. Only 59 entries (14%) had an agent-shaped request with no fingerprint I recognize.

Some of the self-identification goes beyond the user agent string:

- **Claude Code** sends an `X-Request-Id` header on recent builds, in addition to its versioned UA.
- **ZCode** sends `X-Zcode-Trace-Id`.
- **OpenAI**'s GPTBot and OAI-SearchBot started sending an `X-Openai-Host-Hash` header. I don't know what it encodes yet.
- **OpenCode** switched from a generic `Chrome/143.0.0.0` user agent to `OpenCode-User/1.0` on August 19, in a commit literally titled "identify user-initiated web fetches."
- **Exa** signs every request with [Web Bot Auth](https://datatracker.ietf.org/doc/draft-meunier-web-bot-auth-architecture/) (RFC 9421 HTTP message signatures with a `Signature-Agent` header pointing at `https://crawler.exa.ai`). Ahrefs does too. That's 1,475 of this week's 2,306 signal entries (64%) carrying a cryptographic signature. In April, zero did.

OpenCode was an interesting one, because it answered an open question dating back to April. Back then, I saw traffic with OpenTelemetry `Traceparent` and `B3` tracing headers, a Sentry key in a `Baggage` header, and an unusual Accept header: `text/markdown;q=1.0, text/x-markdown;q=0.9, text/plain;q=0.8, text/html;q=0.7, */*;q=0.1`. I attributed it to Cursor. In September, controlled Cursor tests didn't reproduce it, so I downgraded it to "traced proxy agent, identity unknown."

This month, the identical fingerprint showed up with `OpenCode-User` in the user agent. OpenCode's source hardcodes that exact Accept string, its Effect-based HTTP client adds the tracing headers to every request, and its desktop app ships Sentry. Six months of "Cursor" in my April data was at least partly OpenCode. The pre-August builds are still out there: seven requests this week with the old generic UA and the same fingerprint.

The 59 entries (14%) that had an agent-shaped request with no fingerprint I recognize turned out not to be mostly tools with names I haven't curated yet (`LLMSResearchBot`, `web-chunk`, a FindFiles link checker) and people opening `llms.txt` in a browser, plus a few scripts wearing a browser user agent.

My library does have some gaps related to identifying agents hand-writing curl, which is one case where known agents would not self-identify with a user agent string. This week the signal tracker logged 31 curl requests that asked for `Accept: text/markdown`, which nobody types by hand, and in 19 of them the IP had been running Claude Code's own fetch tool within the previous fifteen minutes. That's an agent dropping to the shell mid-session, and the Accept strings vary from one to the next the way a freshly written command would.

## Agents are changing their infrastructure

Cursor has never sent its own user agent string, but what it does send has changed three times this year, and each change came from the infrastructure underneath it rather than from the Cursor client.

In March it was `axios/1.13.5` (or `Python-urllib/3.13`). By April it was a generic Chrome UA with content negotiation (`Accept: text/markdown` first), and requests came in duplicate pairs from two different IPs a second or two apart, which looked like proxy infrastructure. I identified it by behavior after pointing Cursor at a quiet site I control, and wrote a duplicate-request heuristic for it.

In September I ran the same controlled test from a current Cursor build and the duplicate pairs were gone. Each page came in as one request from a different proxy IP with a generic `Chrome/145` UA, a markdown-first Accept header, `Pragma: no-cache`, `Cache-Control: no-cache`, and no client hints. I noticed at the time that Exa's search crawler, `ExaSearchBot`, sent the identical header set with its own name in the UA, and concluded that Cursor's fetch runs on Exa.

On September 30, Exa published a page for a new user agent, [`ExaUser`](https://crawler.exa.ai/exauser), described as the UA it uses "when a user, or an application built on the Exa API, asks for its current content." This morning I ran the controlled test a third time. Four requests arrived on a quiet site I own within fourteen seconds of me hitting enter, and every one of them was ExaUser:

| Time (EDT) | Path | IP |
|---|---|---|
| 11:52:02 | `/` | 152.232.4.179 |
| 11:52:03 | `/robots.txt` | 23.26.71.184 |
| 11:52:14 | `/docs/` | 152.232.4.180 |
| 11:52:16 | `/docs/use-cases/` | 23.26.71.185 |

Same Accept header, same no-cache headers, same rotating proxy IPs in two pools, plus Web Bot Auth signatures. My aggregated metrics show the transition: the confirmed Cursor fingerprint accounted for 34, 11, and 64 requests across my sites in the weeks of September 14, 21, and 28, then 19 the week of October 5, then zero the week of October 12. It didn't go away. It got a name, and the name isn't Cursor.

I don't love this. At the HTTP level, Cursor is now indistinguishable from any other application built on the Exa API, and from Exa's own products. So as of version 0.6.0, my classifier can't attribute anything to Cursor by name. ExaUser is classified as an AI assistant fetching on a user's behalf, alongside ChatGPT-User and Claude-User, and Cursor's traffic is somewhere inside that number. If you want to know how much of your traffic is Cursor, Exa knows and you don't.

The broader point: an agent's traffic signature belongs to whoever runs its fetcher, not to the agent. Cursor outsourced fetching and inherited Exa's identity, Exa's signatures, and (as I'll get to) Exa's manners. Google's agent, it turns out, does the same thing in-house.

## Google's agent arrives as a Google crawler

I went into this check-in believing Google's agents didn't visit my servers at all: that when a user asks Gemini or Antigravity about a page, Google answers from its own index. The week of logs supported that. Google is all over them as a crawler (628 requests from Googlebot, 229 from GoogleOther, 59 from Googlebot-Image, one from Google-Extended across twelve sites) and absent as an agent. Not one request from Gemini CLI, Google-NotebookLM, GoogleAgent-URLContext, GoogleAgent-Chrome, Gemini-Deep-Research, or Google-CloudVertexBot, all of which Google documents and my classifier knows. The string "Antigravity" appeared nowhere in 57,849 lines.

So this afternoon I pointed Antigravity CLI at a quiet site I own and watched the log. Six requests in 68 seconds:

| Time (EDT) | Path |
|---|---|
| 12:35:17 | `/` |
| 12:35:42 | `/docs/` |
| 12:36:12 | `/llms.txt` |
| 12:36:12 | `/index.md` |
| 12:36:24 | `/docs/index.md` |
| 12:36:25 | `/docs/cli/index.md` |

Three things to note about those six lines:

**The user agent is `Google-Jetski-Antigravity`, and that's the whole request.** No `Mozilla/` prefix, no version, no link to a documentation page, and no other headers worth the name: no `Accept`, no `Accept-Language`, plain HTTP/1.1. "Jetski" is Antigravity's internal codename (its docs live at `gweb-jetski.appspot.com`). I couldn't find the token doesn't on any of Google's crawler documentation pages, and it doesn't contain "Gemini," so a site owner who wrote rules for the Gemini user agents Google does document would never match it. My classifier didn't either until an hour ago. The access-log side filed it under uncurated bots because the string contains "Google." The signal side called the `llms.txt` fetch an unidentified agent because every header heuristic I have keys on `Accept`, and there wasn't one.

**None of it came from my machine.** Every request came from `66.249.87.169`, which reverse-resolves to `rate-limited-proxy-66-249-87-169.google.com` and sits in a `/27` in Google's published special-crawlers IP list. That's the same proxy infrastructure Google uses when the Gemini app fetches a URL for you. My own IP made zero requests to the site. Gemini CLI, by contrast, fetched from my IP directly when I tested it in April, and self-identified as `Google-Gemini-CLI/1.0`. In the six months since, my aggregated data shows Gemini CLI at 73 requests total across eleven sites, 64 of them in my own April testing week. Whichever Google agent you use, almost none of its traffic reaches origin servers, and the slice that does arrives from a Google IP range with a Google-flavored user agent.

**It found `llms.txt` on its own.** I asked it about the site; I didn't tell it where anything was. It loaded the homepage, loaded `/docs/`, and then within the same second requested `llms.txt` and the homepage's markdown alternate, followed by the markdown versions of the two docs pages. That's the pattern I [set up for in March](https://dacharycarey.com/2026/03/01/make-hugo-site-agent-friendly/) and [wrote about seeing from Claude Code in September](https://dacharycarey.com/2026/09/23/agent-data-seeking-patterns/): the in-page directive at the top of every page tells the agent there's an index, and the agent goes and reads it. Antigravity read the directive, fetched the index, and switched to markdown for everything after. It did not fetch `robots.txt`.

The detection lesson is the same as Cursor's, from the other direction. Cursor handed fetching to a search company and now looks like a search company's user-triggered fetcher. Google kept it in-house and Antigravity looks like a Google crawler, in the crawler IP range, with a crawler-shaped user agent and none of the request headers a client library would send.

In both cases, the fetch layer decides what your logs see. If you want to know whether Antigravity is reading your docs, you need that exact token in your classifier, because nothing else about the request will tell you.

## Some agents now check robots.txt

I've said in two blog posts previously that agents don't check robots.txt, and that was true at the time but now it's not! In my most recent round of testing, I have observed Cursor fetching robots.txt at fetch time. What I don't know is whether it respects bot-related constraints there - mine don't restrict bots. But it seems at least some agents are getting more sophisticated about checking standard web infrastructure.

This week 3,505 of 57,849 requests (6.1%) were for `robots.txt`. Here's who made them, by my classifier's categories:

| Category | robots.txt requests |
|---|---|
| Search crawlers | 683 |
| AI search | 682 |
| AI crawlers | 678 |
| SEO and marketing bots | 481 |
| Automation wearing a browser UA | 355 |
| Uncurated bots | 214 |
| Social previews | 172 |
| AI assistants | 122 |
| Human | 35 |
| Coding agents | 0 |

The coding-agent row is a true zero. Across Claude Code's 21 builds, GitHub Copilot's two fetch paths, OpenCode, Kiro, ZCode, Qoder, Augment, and the rest, not a single request for `robots.txt`. Antigravity's six requests this afternoon didn't include one either. (Five `robots.txt` requests do land under "agent" once the full pipeline runs, but they belong to a page-audit tool running headless Chrome from Google Cloud addresses, which my signal tracker flags as an unidentified agent because it also fetches `llms.txt`. The trigger fallback can't tell an audit tool from an agent when both reach for the same file.)

The original claim still holds for the agents themselves: an agent making a targeted fetch during a coding session does not stop to read your crawling policy.

The 122 in the AI assistant row are where it gets interesting. Claude-User (Claude.ai fetching on a user's behalf) made 40 of them, DuckAssistBot 44, Amazon Quick 33, and ExaUser 5. One of those five is the request in the table above, made during my Cursor test. Cursor didn't check `robots.txt`; Exa did, on Cursor's behalf, because Exa runs a crawler and its fetch infrastructure behaves like one. OpenAI goes further: OAI-SearchBot has a dedicated user agent variant with a literal `robots.txt` token in it (`OAI-SearchBot/1.4; robots.txt`), which made 132 requests this week doing nothing but fetching that file.

This warrants a correction from some of my earlier posts. When a coding agent outsources its fetching to a search company, your `robots.txt` reaches that agent's users whether the agent intended it or not. But I can't tell whether the agent honors it. Exa's ExaUser page says the UA "is not used to determine whether content may appear in Exa search" and points opt-outs at `ExaSearchBot` instead, which reads to me as: we fetch it, we don't promise to obey it for live fetches. My sites don't restrict any bots, so I have no data either way. If yours do, the test is cheap: block ExaUser in `robots.txt`, paste one of your URLs into Cursor, and see whether the page comes back.

## What to take away

If you care about agent traffic to your websites, you need to keep in mind this is a moving target. The landscape has changed pretty dramatically in six months, and will likely continue to change with more companies releasing their own agents or consolidating infrastructure under various providers. If you pay a vendor for traffic metrics, make sure they're keeping up with changes to agents - new releases, changes to tracking metrics, and deprecations - and keeping your metrics up-to-date as a result.

If you have your own server logs, you could try running them through my [agent-traffic-classifier library](https://github.com/agent-ecosystem/agent-traffic-classifier) - it's open-source and free to use - to get a comparison to what your vendor reports. For my own websites, I do some additional infrastructure stuff to capture detailed header sets, which not all server logs provide without extra work, so you may not see results as rich as mine.

If we see more agents behaving like crawlers when performing web fetch, it's worth watching whether agents start respecting `robots.txt` constraints. Today's Cursor test is the first time I've seen an agent fetch `robots.txt`, so I've been saying for months that they will browse to any page they think they need regardless of what `robots.txt` says. If more agents start to behave like crawlers, and respect standard web conventions, we'll likely see agent fetch behavior change.
