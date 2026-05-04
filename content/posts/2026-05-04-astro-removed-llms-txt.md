---
title: Astro Removed its llms.txt
author: Dachary Carey
layout: post
description: In which Astro removes its llms.txt entirely, and I explore the result.
date: 2026-05-04 07:00:00 -0500
url: /2026/05/04/astro-removed-llms-txt/
image: /images/astro-removed-llms-txt-hero.jpg
tags: [ai, documentation]
---

As I have been re-scoring documentation sites with my updated `afdocs` tool, I have had the opportunity to see a snapshot of various docs sites a few weeks apart as the industry is starting to pay a lot more attention to how to make documentation agent-friendly. I got a surprise when I re-scored the docs for [Astro](https://docs.astro.build/en/getting-started/), a web framework that is well-regarded. In the prior scoring run, Astro got a C (78) for agent-friendliness; their new score is an F (59); capped by a missing llms.txt. I wanted to dig into why someone would make a change to make their docs *less* agent-friendly, when you have a contrast like last week's GitHub going out of its way to build their llms.txt into a comprehensive [agent-discovery layer](https://dacharycarey.com/2026/05/01/github-docs-api-llms-txt/).

First, a bit of context. Between my April 11 and May 2 scoring runs, Astro removed all of their `llms.txt` files: the root `/llms.txt`, `/llms-full.txt`, `/llms-small.txt`, and per-section files at `/_llms-txt/api-reference.txt` and `/_llms-txt/how-to-recipes.txt`. The change happened in [PR #13538](https://github.com/withastro/docs/pull/13538) on April 20, authored by Chris Swithinbank (a core Astro maintainer) and approved by another maintainer. The PR removed the entire `Starlight llms.txt plugin` build pipeline that had been added in April 2025.

What was there before was *good*. Astro had shipped a layered, progressive-disclosure `llms.txt`: an abridged version, a full version, and topical files for API reference and how-to recipes. That's almost exactly the structure my [spec](https://agentdocsspec.com) recommends for large docs sets. They weren't doing the bare minimum; they were doing it well.

So why remove it?

## Astro's reasoning, explained

The PR description is short, so it's worth quoting in full:

> Currently we generate a range of Markdown files including `llms-full.txt` and `llms-small.txt` for every build. However, we've seen little uptake recently in usage — these files get very little traffic. Instead we can focus efforts on the [MCP server](https://github.com/withastro/docs-mcp) and perhaps in the future offer per-page Markdown content.
>
> Apologies to anyone who was relying on these! AI trends move quickly 😢

There's also a secondary justification in the PR: the build pipeline took ~44 seconds to generate these files, so removing them shaved time off every CI run.

Astro made three claims here, and I think they deserve exploration:

1. **Low traffic / low uptake.** They didn't share numbers, but the implication is that these files weren't getting enough use to justify keeping them.
2. **The MCP server is the replacement.** [`withastro/docs-mcp`](https://github.com/withastro/docs-mcp) is real, live, and maintained.
3. **Per-page markdown is a future possibility.** Floated, but not implemented today.

The "AI trends move quickly" framing is the part that stuck with me. It positions `llms.txt` itself as a trend that has passed. That's a strong editorial position from a maintainer of one of the more well-regarded modern docs frameworks. I think usage count puts the wrong emphasis on the value of this file.

## Metrics don't tell the whole story

The "low traffic" claim is the one that bothers me most, because it depends entirely on how you measure.

I have imagined `llms.txt` as a navigation index for agents. It emerged as a [proposal](https://llmstxt.org) framed for LLM training crawlers and inference. That use case never really materialized. The major training crawlers don't appear to consume it. What *has* started to happen, slowly, is that `llms.txt` is being repurposed as an agent navigation layer. This is something I've been [writing about](https://dacharycarey.com/2026/02/18/agent-friendly-docs/), and Anthropic is doing it on every single Claude Code docs page. But because the original framing was about training data, a lot of teams measuring "uptake" are probably still measuring against the old use case, and reaching the conclusion that nobody uses it.

It's not something a human is going to load in a browser. It's not part of an organic search funnel. And critically, agents don't know to fetch it unless something tells them about it. You can put a perfect `llms.txt` at your site root, and if no documentation page contains an in-page directive pointing to it (the `llms-txt-directive-html` and `llms-txt-directive-md` checks in my [spec](https://agentdocsspec.com)), then agents will simply never look. They retrieve URLs from training data and fetch what they need. If agents can't find what they need, they bounce to another source. The index file sits there, unread, until either a human or another agent points to it.

A quick check of the captured snapshot: Astro's docs pages did not include an in-page directive linking to the `llms.txt`. They were missing the guidepoint to tell agents how to discover and use the file. Without the directive, "low uptake" is the expected outcome regardless of how good the index is.

So the question I'd want to ask is: what was Astro measuring? Raw page views are roughly the wrong yardstick. What you'd want is server logs filtered for agent user-agents (`Claude-User`, `ChatGPT-User`, `cursor-agent`, and the rest), correlated against sessions where an agent was actively helping a user build an Astro site. That's a much harder signal to capture, and the PR description doesn't suggest that's what was measured.

## The space is messy

I want to be fair to Astro here. The decision to repurpose `llms.txt` as an agent navigation layer is *my* decision (and a few other people's), based on watching agents succeed with it in real workflows. There's no IETF working group. There's no formal RFC. The proposal at `llmstxt.org` is one person's draft. My [spec](https://agentdocsspec.com) is one person's interpretation of how agents actually use docs, grounded in empirical observation but still mine. Reasonable people can read the same evidence and come to different conclusions about whether `llms.txt` is the right primitive.

Astro presumably didn't know about my work, and shouldn't have to. They saw a build pipeline producing files that, by their measurement, weren't being used, and they had a competing investment (the MCP server) that they believed was a better bet. From inside that frame, removing the unused thing is reasonable engineering.

I think the gap here is in the audience model. The PR treats "AI tooling" as roughly one audience, and proposes the MCP server as the replacement for the removed files. That framing is reasonable on its face, but it collapses two populations that I think behave differently in practice. The MCP server is great for users who run an agent harness that supports MCP, who know about Astro's MCP server, and who have configured it. That's a narrow population today, but it might be a wider one tomorrow. `llms.txt` works for any agent that can fetch a URL, including the long tail of less capable, less configured, less curated agent workflows that I keep watching fail in interesting ways. The PR doesn't suggest Astro looked at those as separate populations; the "instead" framing implies a one-for-one substitution, and that's the part I'd push back on.

## The practical impact on agents

In my afdocs scoring, the change cost Astro 19 points: from 78 (C) to 59 (F). The F isn't a gradient; it's a hard cap. When `llms-txt-exists` fails, the [score is capped at 59](https://afdocs.dev/checks/content-discoverability) regardless of what else is true about the site. That cap exists because, in my testing, no other discovery mechanism comes close to working as well for agents.

What did Astro actually lose, from the agent's perspective?

**Agents now have to guess URLs from training data.** This is the failure mode I covered at length in the [original article](https://dacharycarey.com/2026/02/18/agent-friendly-docs/). Without an authoritative index, agents fall back on memorized URL patterns and made-up URLs. Sometimes they hit the right page; often they don't. Astro is a relatively new framework with a fast-evolving API surface, which makes this worse: training data ages quickly, and there's no signpost on the live site telling agents where the current canonical content lives.

**Agents have no way to get markdown either.** `docs.astro.build` doesn't serve `.md` URL variants (`/en/getting-started.md`, `/en/getting-started/index.md`, and similar all return 404), and `Accept: text/markdown` content negotiation returns HTML. So an agent that lands on an Astro docs page gets the HTML version, has to extract content through whatever its harness does to convert HTML to markdown, and has no way to request a cleaner format. The PR mentions per-page markdown as a "future possibility," which would close part of this gap, but it's not implemented today.

**The other Astro-friendliness signals still pass.** Page sizes are reasonable, the structure is clean, URLs are stable, no auth gates. So the site isn't *broken* for agents; it's just no longer discoverable as agent-friendly. An agent that finds its way to a specific Astro docs page with the right URL in hand will have a fine experience. An agent that needs to navigate from "I need to know how Astro content collections work" to the relevant page is back to guessing.

There's a gap on my side here too: afdocs has no signal for "this site has a working MCP server." From the score's perspective, Astro's investment in `docs-mcp` is invisible. That overstates the regression for users whose agent harness supports MCP and has Astro's server configured. I'm thinking through whether to add an MCP discovery probe to afdocs (something like `/.well-known/mcp` or scanning docs pages for `mcp://` references), but I haven't landed on the right shape yet. For now, the scoring methodology measures discoverability via web fetch, and that's the audience that lost something here.

## The MCP as replacement argument

The MCP-as-replacement framing assumes that MCP and `llms.txt` solve the same problem for the same audience. I don't think they do.

MCP requires three things to work: an agent harness that supports MCP at all, a user who knows the MCP server exists, and a user who has configured it. Today, that's a much narrower segment than "any HTTP client that can fetch a URL." Some specifics worth being explicit about:

- **Many coding agents don't support MCP.** Cursor and Claude Code do. GitHub Copilot's support is partial and evolving. ChatGPT has its own equivalent. A lot of smaller or more specialized agent tools don't support MCP at all. If your strategy is "the MCP server is how agents will use our docs," you've selected for one specific cohort of agent users.
- **MCP servers have to be installed and configured per-user.** Even on platforms that support MCP, the user has to know that `withastro/docs-mcp` exists, decide to install it, and configure their harness. That's a non-trivial step in the middle of a development workflow. Most people building an Astro site for the first time will not stop and install an MCP server before asking their agent for help.
- **Enterprise environments often restrict or forbid MCP.** I've heard from people at multiple large organizations that they can't install third-party MCP servers because of data exfiltration or supply-chain concerns. MCP is code that runs locally, with whatever permissions the user grants it. That's a higher bar than "the agent fetched a URL," and some environments draw the line below it.
- **The "selection bias" problem.** Anyone who has gone to the trouble of installing the Astro MCP server is, by definition, a power user who is invested in Astro. They're not the population most in need of help discovering docs. The users most likely to fall through the cracks (newer developers, casual users, people exploring Astro for the first time) are the ones who won't install the MCP server.

`llms.txt` is the opposite shape. It's a flat file at a well-known URL. Any agent that can issue an HTTP GET can use it. There's no installation step, no configuration, no platform requirement. The ceiling is lower (you can't do much beyond linking to pages), but the floor is much, much lower too. Casual users get the benefit by default.

This is the same trade-off [I wrote about with GitHub's API-based llms.txt](https://dacharycarey.com/2026/05/01/github-docs-api-llms-txt/) a few days ago, in a different shape. GitHub built a richer experience that capable agents can use; Astro is doing something similar with the MCP server. In both cases, the richer experience is real and worth investing in. But replacing the simpler primitive with the richer one (rather than offering both) means the people most in need of the simpler primitive get less help, not more.

The cost of keeping a `llms.txt` alongside the MCP server is low. The build pipeline took 44 seconds. The file format is a flat list of links. There's no real conflict between the two; they serve different agent populations at different points on the capability curve. Removing one to focus on the other isn't a refinement, it's a narrowing of who gets served.

## Where this leaves us

Astro is not a bad actor here. They saw a build artifact they believed wasn't being used, they had a competing bet they wanted to invest in, and they made a defensible engineering call. The "apologies to anyone who was relying on these" line in the PR makes clear they knew there was *some* consumer; the "instead, we can focus efforts on the MCP server" framing makes clear they consider that consumer to be served by the MCP server going forward. I disagree, but the disagreement is about how finely you should decompose your AI audience, not about whether Astro cared about that audience at all.

What I'd love to see is more explicit measurement. If you're a docs team thinking about whether your `llms.txt` is "worth it," the question to ask isn't "how many page views does it get?" It's "how many agent sessions touched it, and what happened next?" That's a harder signal to extract, but it's the one that actually answers the question. And as I [keep arguing](https://dacharycarey.com/2026/03/12/vibes-out-data-in/), reasoning from prior expectations about what agents do is not the same as testing what they actually do.

I'd also love to see Astro keep the door open. Per-page markdown is mentioned in the PR as a future possibility. Adding an in-page directive pointing to a (rebuilt, smaller) `llms.txt` would cost almost nothing and would catch the agent population that the MCP server doesn't reach. The two approaches don't have to be mutually exclusive, and the cost of supporting both is low compared to the cost of being undiscoverable to a meaningful slice of the audience.

For everyone else: if you're considering removing your own `llms.txt` because it "doesn't get traffic," please measure twice. The traffic you're not seeing might be the traffic you most wanted.
