---
title: GitHub Reimagined llms.txt as an API
author: Dachary Carey
layout: post
description: In which GitHub replaces the standard llms.txt URL list with a purpose-built agent API.
date: 2026-05-01 07:00:00 -0500
url: /2026/05/01/github-docs-api-llms-txt/
image: /images/github-docs-api-llms-txt-hero.jpg
tags: [ai, documentation]
---

I'm working on a new research report that starts by scoring a wide cross-section of documentation sites with [afdocs](https://afdocs.dev/). Consider it an industry-wide audit driven by specific research questions. This has given me an excuse to dig into different platforms, web hosts, documentation sizes, and documentation strategies. I've really been surprised and intrigued by the different approaches I've seen in the wild, and I have a rich editorial calendar of content to share with you about these findings (when I get the time).

But for today, I want to talk to you about something really cool I found GitHub doing, and whether it works at all for the intended purpose. When I ran my scoring tool on the [GitHub Pages product documentation](https://docs.github.com/en/pages), my tool returned a "single page score cap." This message indicates it can't find documentation content from an llms.txt or a sitemap fallback. But my tool *also* checks for `llms-txt-exists` - and it found an llms.txt at [https://docs.github.com/llms.txt](https://docs.github.com/llms.txt). So of course I wanted to know what was in that file if my tool couldn't find any documentation URLs to sample.

Here's what I found: GitHub Docs has replaced the standard llms.txt format with something entirely different, and my scoring tool doesn't know what to do with it. So my next question was: what would an agent do with it?

## What a standard llms.txt looks like

The [llms.txt spec](https://llmstxt.org) describes a simple convention: a plain text file at `your-docs-site.com/llms.txt` that lists URLs to your documentation pages, optionally with descriptions. Agents can fetch the file, discover your pages, and know where to look for content. Most implementations are exactly that, a flat list of links, sometimes structured with headers for sections. Multi-product websites with large documentation sets spanning many projects often implement node/leaf structures for these files, with a central file enumerating all of the product-specific files, and then each product having its own file with the product documentation links.

Here's what the llms.txt looks like for this website, where you're reading this article:

```markdown
# Dachary Carey

> Tools, apps, and strong opinions about coffee.

## Pages

- [About](https://dacharycarey.com/about/index.md)
- [AI & Agent Research](https://dacharycarey.com/ai-research/index.md)
- [Documentation & Developer Education](https://dacharycarey.com/documentation/index.md)
- [Programming](https://dacharycarey.com/programming/index.md)
- [Resume](https://dacharycarey.com/resume/index.md)

## Posts

- [What an Agent Score Can Tell You](https://dacharycarey.com/2026/04/18/what-agent-score-can-tell-you/index.md): In which I investigate what agent scoring tools actually measure.
- [Designing an Agent Reading Test](https://dacharycarey.com/2026/04/06/designing-agent-reading-test/index.md): In which I try to give people tools to understand how agents read web content, and where they fail.

...etc...
```

Agents work well with markdown, and by organizing the document with headings, article titles, and brief descriptions, agents can easily find the information they're looking for.

GitHub has done something arguably very much cooler - but not matching the spec.

## What GitHub Docs has instead

Fetch `docs.github.com/llms.txt` and you get this:

```markdown
# GitHub Docs

> Help for wherever you are on your GitHub journey.

## How to Use

To find a specific article, use the **Search API** with a query. To browse all available
pages, use the **Page List API** to get a list of paths, then fetch individual articles
with the **Article API**. The `/api/article/body` endpoint returns clean markdown, ideal
for LLM consumption.

## APIs

- [Page List API](https://docs.github.com/api/pagelist/en/free-pro-team@latest): Returns
  all article paths for a given version. Use this to discover what content is available.
- [Article API](https://docs.github.com/api/article): Fetches a single article as JSON
  (metadata and markdown body). Use `/api/article/body` for markdown only.
- [Search API](https://docs.github.com/api/search/v1): Full-text search across all articles.
- [Versions API](https://docs.github.com/api/pagelist/versions): Lists all available versions.
- [Languages API](https://docs.github.com/api/pagelist/languages): Lists all available languages.
```

No page URLs. No links to documentation. Just an API contract and the instructions.

The data is all there. The Page List API returns a newline-delimited list of every article path in the GitHub Docs — including the 28 GitHub Pages articles we were scoring. The Article API returns structured JSON with metadata and a markdown body. The Search API lets you query the full content. GitHub has built a richer system than a flat llms.txt. This is purpose-built agent infrastructure that goes well beyond what the spec imagined.

But my scoring tool, `afdocs`, expects a URL list. It found no HTML or markdown links in the llms.txt, discovered only the root page, and triggered the `single-page-sample` cap. My tool is designed to parse llms.txt files that match the spec, and this doesn't.

So I asked myself: what does this mean for agent usage patterns? The *reason* I wrote the [Agent-Friendly Documentation Spec](https://agentdocsspec.com) and the companion `afdocs` tool is to help documentation teams understand how agents actually discover and use documentation content, to make it more agent-friendly. So was this a new capability that my tool should recognize? Should the spec be updated to recommend something like GitHub's approach to llms.txt?

## What would an agent actually do with this?

This is *the* question. If GitHub added an agent-facing directive to their HTML pages pointing to this llms.txt, and an agent encountered it, what would happen?

A capable agent, like a Claude Code Sonnet or Opus running with tool-use enabled, capable of chaining HTTP calls, would probably work through it. The instructions are clear enough: hit the Search API for specific queries, use the Page List API to browse, call `/api/article/body` for clean markdown output. This is a multi-step workflow, but it's not a hard one for an agent that can reason about API contracts and make sequential requests.

But a simpler agent is a different story. I discovered in my testing that the Claude Code `WebFetch` tool uses a smaller model (Haiku) to fetch web content in a background process. The main agent that the user interacts with, Sonnet or Opus, hands a URL and a prompt to the smaller model, which fetches it and returns the content to the orchestrating agent. That subagent isn't reasoning about the API contract. It fetches the llms.txt, returns the text, and waits for further instruction. Unless the orchestrating agent explicitly says "now call the Page List API, then call the Article API for each path," the chain stops there. The subagent won't spontaneously recognize a multi-hop API workflow and execute it unprompted.

But even beyond that, what the subagent returns to the foreground agent is entirely dependent on two factors: what text the foreground agent used in the prompt, and how the subagent interprets the prompt and the content. And that is a non-deterministic loop that means results are inconsistent. For example, the foreground agent might say: "find me documentation links related to getting started from this URL: llms.txt." (It's more structured than this, but you get the idea.) The subagent goes to llms.txt, and doesn't see *anything* there related to getting started.

The subagent then has a choice: tell the foreground agent that the content isn't present in the page. *Or* maybe it's a *very* good day and the subagent does some actual thinking instead of just checking for a specific string or concept, and says to the foreground agent "hey, there's something else here." That second scenario is unlikely; I *have* seen Haiku surface interesting findings on occasion to the foreground agent, but that's the exception, not the rule. (This is also why the agent directive that my spec recommends you include on each page isn't foolproof; Haiku has to recognize it's there *and* tell the foreground agent it exists, neither of which is a given if Haiku does the simple thing and just checks whether the content that it was asked to retrieve is present.)

Different platforms handle this differently. Rhyannon Rodriguez has been doing some very cool [web fetch retrieval research](https://rhyannonjoy.github.io/agent-ecosystem-testing/) across agent platforms. Most of them seem to use a more limited subagent to perform the fetches (it's cheaper), and have some filtering mechanism in place to reduce the volume of content handed back to the foreground agent. This is a sensible strategy to preserve the context of the agent that the user is interacting with - the orchestrator - but it does mean the retrieved content really has to match what the foreground agent asked for, or the subagent may discard it as irrelevant before the "reasoning" agent ever sees it.

So back to the GitHub case. The standard llms.txt format sidesteps this entirely. A flat list of URLs hands the agent something it can act on directly, without reasoning about what the links mean or chaining API calls. Any agent that can fetch a URL can use it. In other words, an agent asking for the llms.txt has some idea what it expects, can formulate a prompt for the agent performing the fetch like "find links related to getting started", and can get back some list of pages to fetch. But because the GitHub llms.txt doesn't contain content that matches the agent's query, it's an open question what the fetching agent will return and whether the more capable reasoning agent ever gets any idea that a richer experience is available to help it discover content.

## A richer system that fewer agents can use

To be clear, GitHub has invested seriously in agent-accessible docs. The Article API returns clean JSON with metadata and a markdown body, with a `/body` variant that strips the wrapper for agents that just want the content. The Search API returns full-text results with context snippets. The Page List API enumerates every article path. There's a Versions API and a Languages API. And every documentation page on `docs.github.com` honors `Accept: text/markdown` and serves real markdown back. This is real investment in agent infrastructure.

But the llms.txt format decision looks to me like a tradeoff, not an upgrade. The standard llms.txt format hands a agents something I've watched them act on with no reasoning required: fetch the file, get a list of URLs, fetch the URLs. Any agent that can issue an HTTP request can probably use it. GitHub's version requires the agent to read prose instructions, understand that "Page List API" maps to a specific endpoint, plan a multi-hop sequence, and execute it. Capable agents with tool-use *can* do that, if they choose to. Subagents performing background fetches probably can't, or won't, because they're typically scoped to retrieving the content the orchestrator asked for, not to designing new workflows.

One thing to note: these two formats don't have to be mutually exclusive. The Page List API already returns a flat list of paths, one per line. GitHub could plausibly generate a spec-conformant llms.txt from that same data: convert each path to a URL, group by section, ship it as part of `llms.txt` alongside the current API instructions. Tools that expect the standard format would have something to work with. Agents that can handle the richer API would still have it. Adding the expected information into the file would cost very little and would likely meet less-capable agents where they are.

Without that, GitHub may have built infrastructure optimized for the agents that need it least. The Sonnets and Opuses of the world can figure out the API contract. The smaller models doing the actual fetching, on every major agent platform, are more likely to see a file that doesn't match what they were asked to find and return nothing useful. But that's a hypothesis based on what we know about how these systems work, not a tested result.

## Test with real agents

Everything I've said above about how agents are likely to handle GitHub's API-based llms.txt is reasoning from prior behavior, not from my own tested results. That's exactly the gap I've been [writing about](https://dacharycarey.com/2026/03/12/vibes-out-data-in/), and I have to acknowledge it for my own article here.

GitHub's approach is novel and interesting. It's a thoughtful answer to a real problem. A flat URL list at the scale of GitHub Docs would be enormous, and most large doc sites that ship one end up with a truncated index that agents can only partially see. The API contract, the markdown body endpoint, the version and language support, the full-text search: that's a serious investment in agents as a first-class consumer.

What I don't know is whether agents in the wild can actually use it. Two questions worth answering separately: do the agents fetching your docs actually have the problem you're solving for, and can they actually use the solution you've built? Both are empirical questions, and neither one can be answered by reasoning from how the system *should* work. You have to put the thing in front of real agent platforms, on the harnesses real users are running, and see what happens.

I haven't tested this with the popular coding agents, but would love to do so. That's the kind of research I want to do, and it's the kind of test that would benefit from being run across multiple platforms (Claude Code, Cursor, Copilot, ChatGPT, Codex) with the same prompt, to see which agents recognize the API contract, which stop at the llms.txt content, and which silently return nothing. But all this agent-related poking is stuff I am doing outside of my day job, while also maintaining multiple open source tools, so the available time for research has been diminishing. So I don't know when it will happen!

If you've tried using GitHub's docs through your agent of choice and noticed it succeeding or failing in interesting ways, I'd love to hear about it. And if I get to the testing first, I'll write up what I find.
