---
title: How to Evaluate a Platform-Written Spec
author: Dachary Carey
layout: post
description: In which I find that a platform-published spec's omissions track financial incentives.
date: 2026-03-28 15:00:00 -0500
url: /2026/03/28/how-to-evaluate-platform-written-spec/
image: /images/how-to-evaluate-platform-written-spec-hero.jpg
tags: [ai, documentation]
draft: false
---

Recently, Vercel published an [Agent Readability Spec](https://vercel.com/kb/guide/agent-readability-spec) (Timothy Jordan, last updated March 23, no publication date), a scoring system for how well websites serve AI agents. It covers llms.txt, markdown mirrors, content negotiation, structured data, robots.txt, and a checklist of other recommendations. Four days later, they followed up with ["Make Your Documentation Readable by AI Agents"](https://vercel.com/kb/guide/make-your-documentation-readable-by-ai-agents) (Rich Haines; March 27), a companion implementation guide that references the spec article and adds its own recommendations. If you're a documentation team trying to make your site work better for coding agents, either article looks like a useful starting point.

I've been working on this problem since February. My [Agent-Friendly Docs research](https://dacharycarey.com/2026/02/18/agent-friendly-docs/) started with 10+ hours of hands-on testing with Claude, validating 578 coding patterns against real documentation sites. That research turned into the [Agent-Friendly Documentation Spec](https://agentdocsspec.com), an open specification with 22 checks, empirical thresholds, and a [companion CLI tool](https://github.com/agent-ecosystem/afdocs) for running those checks against any site. The spec was first committed on February 21 and has been iterated publicly with community contributions since then.

When I read Vercel's articles, I noticed overlap with my spec. The combination of llms.txt as a primary discovery mechanism, markdown mirrors via `.md` URLs, `Accept: text/markdown` content negotiation, code fence validation, HTTP status and redirect behavior, and a scoring system for evaluating all of these together is the framework I published a month earlier. But I also noticed what was *missing*. And when I started mapping the omissions against Vercel's platform and business model, I spotted a pattern. I want to talk to you about a question every documentation team should ask: whether the recommendations you're following serve your users or the platform publishing them.

## What overlaps

Both specs recommend the same core practices. If you read only the overlapping portions, you'd come away with a similar action list:

| Recommendation | Agent-Friendly Docs (Feb 21) | Jordan article (Mar 23) |
|---|---|---|
| Serve `llms.txt` as primary discovery | 5 checks with size limits, link validation, progressive disclosure | "Serve llms.txt at root" |
| Markdown mirrors via `.md` URLs | `markdown-url-support` check | "Provide markdown mirrors for HTML pages" |
| `Accept: text/markdown` content negotiation | `content-negotiation` check | "Support Accept: text/markdown" |
| Code fences with language identifiers | `markdown-code-fence-validity` check | "Fence all code blocks with language identifiers" |
| HTTP status and redirect behavior | `http-status-codes` + `redirect-behavior` | "Return HTTP 200 with 0-1 redirects" |
| Scoring system | Pass/warn/fail per check with defined thresholds | Percentage scale (0-100) |

Individual pieces of this existed before either spec. Jeremy Howard proposed llms.txt in [September 2024](https://llmstxt.org/). Checkly published research on [agent content negotiation](https://www.checklyhq.com/blog/state-of-ai-agent-content-negotation/). But the combination of these elements into a unified evaluative framework with a scoring system is, as far as I can find, something I published first.

The Jordan article's "Further Reading" section cites four generic standards: llmstxt.org, schema.org, openapis.org, and robotstxt.org. The Haines article links to the Jordan article three times but likewise cites no external research. Neither article references the Agent-Friendly Docs spec, this site, or the GitHub repository.

I'll leave the attribution question there. I want to focus on the more useful question: **what did Vercel leave out, and why?**

## What Vercel omits

My spec has 22 checks across 8 categories. Many of them don't appear in either Vercel article. Here's what's missing:

- **SPA/client-side rendering detection.** Pages built with client-side rendering return empty shells to coding agents. The agent gets a 200 response with navigation chrome but zero documentation content. This is worse than truncation because the agent doesn't know anything is missing.
- **Truncation budget analysis** with platform-specific limits. Claude Code truncates at ~100K characters. MCP Fetch defaults to 5K. Cursor varies between 28KB and 240KB+ depending on the fetch method. These numbers matter for sizing your content. Research into platform truncation limits is ongoing; check out [the research Rhyannon Rodriguez has been doing](https://rhyannonjoy.github.io/agent-ecosystem-testing/) for more details.
- **Content-start-position.** How far into the HTML response does actual documentation content begin? On some sites, the answer is 87% of the way through, because inline CSS and navigation chrome fill the truncation budget before the first paragraph.
- **llms.txt size limits** with defined thresholds. My spec recommends under 50K characters for a pass, with a progressive disclosure pattern for large documentation sets.
- **llms.txt freshness monitoring.** Does your llms.txt actually reflect what's on your site?
- **Markdown-content parity.** Do your markdown and HTML versions contain the same content?
- **Cache header hygiene.**
- **The distinction between coding agents and training crawlers.** My spec explicitly scopes to real-time coding agents (Claude Code, Cursor, Copilot) and excludes training crawlers (GPTBot, ClaudeBot). Both Vercel articles conflate them.

That last point deserves its own section.

## Coding agents and training crawlers are not the same audience

The Jordan article recommends configuring `robots.txt` to allow GPTBot, ClaudeBot, CCBot, and Google-Extended. The Haines article doubles down with a user-agent detection system for "Claude, ChatGPT, GPTBot, Cursor, Copilot." Both claim to address coding agents, but the mechanisms they describe primarily catch training crawlers. These are fundamentally different audiences.

Coding agents use generic HTTP libraries or embedded browsers. When I did my research earlier this month, Claude Code identified itself as `axios/1.8.4`. Cursor sent `axios/1.13.5` (or fell back to `Python-urllib/3.13`). GitHub Copilot used the VS Code embedded browser. The versions may have changed by now, but the implementations probably haven't. These tools are indistinguishable from normal developer tooling or browser traffic. They don't check `robots.txt` because they're not crawlers; they're HTTP clients making targeted requests during a development session.

Training crawlers (GPTBot, ClaudeBot, etc.) are separate infrastructure that scrapes the web for model training data. They identify themselves with specific user-agent strings. They respect `robots.txt`. They have fundamentally different access patterns, different frequencies, and different purposes.

An audit cited in my spec ([Longato, August 2025](https://www.longato.ch/llms-recommendation-2025-august/)) found zero visits to `llms.txt` from training crawler user-agents across 1,000 domains over 30 days. A separate 90-day study ([OtterlyAI GEO Study](https://otterly.ai/blog/the-llms-txt-experiment/)) found only 84 requests (0.1%) to `/llms.txt` out of 62,100+ AI bot visits. Training crawlers don't appear to use `llms.txt`. The agents that *do* use it are the coding agents that `robots.txt` can't see.

When articles conflate these two audiences, they produce advice that sounds reasonable but misses the actual problem. Configuring `robots.txt` for AI bots is about training data policy. Making your docs readable for coding agents is about page size, rendering strategy, content format, and truncation limits. These are different problems requiring different solutions.

## Mapping omissions to platform incentives

A caveat before I dig in: I'm not a Vercel platform expert. The analysis below is based on a few hours of reading Vercel's public documentation and blog posts. If I've gotten something wrong about how their platform works, I'm happy to correct it.

That said, when I looked at each omission from the Jordan article and asked "does this align with a Vercel platform limitation, a missing capability, or a business interest?", the answer was yes in several cases.

### Content-start-position would spotlight Next.js HTML bloat

Next.js is Vercel's flagship framework. Vercel's own [document size optimization guide](https://vercel.com/kb/guide/how-to-optimize-your-document-size-in-next-js) recommends using Tailwind or CSS Modules instead of runtime CSS-in-JS because the latter "often inject `<style>` tags into your HTML at runtime" leading to "significant bloat."

Vercel's own blog post on [content negotiation](https://vercel.com/blog/making-agent-friendly-pages-with-content-negotiation) (Zach Cowan, Mitul Shah; February 3, 2026) acknowledges: "The HTML version of this page is around 500KB. The markdown version is 3KB, a 99.37% reduction in payload size." That's the content-start-position problem in one sentence. If 99% of your HTML payload is not documentation content, a content-start-position check would formalize a known issue with Next.js sites.

Vercel's workaround is their [inline `<script type="text/llms.txt">` proposal](https://vercel.com/blog/a-proposal-for-inline-llm-instructions-in-html), which places agent instructions near the top of the document. That proposal exists *because* the content is buried. A spec check that measured where content begins would quantify the problem their workaround addresses.

### llms.txt size limits would fail Vercel's own properties

Vercel's own `llms.txt` at `vercel.com/docs/llms.txt` is 336 KB, roughly seven times my spec's 50K character threshold. The AI SDK's `llms.txt` at `ai-sdk.dev/llms.txt` is approximately 4.6 MB. These are the discovery index files that agents can use to find relevant documentation pages, not the full-content companion files (`llms-full.txt`) that are expected to be large.

A [GitHub issue (#4355)](https://github.com/vercel/ai/issues/4355) on the AI SDK requested they "reduce llms.txt size to fit o1 context window." The issue was closed with no discussion, but the file is still 4.6 MB. Their AGENTS.md files have the same problem: the react-best-practices skill ships a 2,975-line / 83KB AGENTS.md that was [called out in issue #169](https://github.com/vercel-labs/agent-skills/issues/169) for lacking progressive disclosure.

My spec recommends under 50,000 characters for a pass. Claude Code's truncation limit is ~100K characters. Vercel's own llms.txt files far exceed these thresholds. Including size limits in their spec would mean their own properties fail.

### The agent/crawler distinction conflicts with Vercel's bot management revenue

Vercel's blog post ["The Three Types of AI Bot Traffic"](https://vercel.com/blog/the-three-types-of-ai-bot-traffic-and-how-to-handle-them) (Kevin Corbett; August 13, 2025) categorizes traffic as: "AI training crawlers scan everything," "AI engine grounding bots fetch real-time updates," and "AI referrals bring high-intent visitors." Coding agents are not mentioned as a category at all.

Vercel's [bot management system](https://vercel.com/docs/bot-management) (opt-in, not on by default) identifies "clients that violate browser-like behavior" and serves "a javascript challenge to them." The [Challenges docs](https://vercel.com/docs/vercel-firewall/firewall-concepts#challenge) are explicit about the consequences: "Direct API calls (e.g., from scripts, cURL, or Postman) will fail if they require challenge validation." As established above, coding agents are non-browser HTTP clients. They can't solve JavaScript challenges. If you enable Vercel's bot management, you are blocking coding agents. Their docs describe these as limitations, but don't make the explicit connection that you'll be blocking coding agents.

Vercel's docs mention that site owners can create custom WAF bypass rules for trusted automated traffic. But as we've seen, coding agents don't send identifiable user-agent strings. There's no stable pattern to match on. You can't write a bypass rule for traffic you can't identify.

Vercel sells bot management and WAF features at enterprise pricing. Their own blog post ["The Rise of the AI Crawler"](https://vercel.com/blog/the-rise-of-the-ai-crawler) (December 17, 2024) reports GPTBot, Claude, AppleBot, and PerplexityBot combining for "nearly 1.3 billion fetches" across their network. Per Vercel's [WAF pricing docs](https://vercel.com/docs/vercel-firewall/vercel-waf/usage-and-pricing), standard custom WAF rules charge Edge Request and Fast Data Transfer fees even for denied requests: "When a custom rule is active, you incur usage for every challenged or denied request." (Vercel notes an exception for "persistent actions," which do not incur these fees.)

A spec that correctly and explicitly defined the distinction between helpful coding agents and training crawlers would create pressure on Vercel to handle them differently. Right now, their bot management treats them as one undifferentiated category of non-browser traffic. Documentation teams that enable bot management on Vercel would block coding agents with no way to selectively allow them through.

### Truncation analysis and freshness monitoring would spotlight missing tooling

I couldn't find any offering by Vercel providing native analytics for agent traffic. Their Web Analytics and Speed Insights are JavaScript-based, which means they don't see any bot or agent traffic at all. Third-party solutions like [Profound Agent Analytics](https://vercel.com/marketplace/profound) have emerged on their marketplace, but even Profound tracks "AI crawlers from major platforms" by analyzing server-side logs for known user-agent strings. That catches ChatGPT, Claude (the crawler), Google, and Perplexity. It doesn't catch coding agents, which send generic user-agents. The gap between "AI bot analytics" and "coding agent analytics" is the same crawler/agent conflation showing up at the tooling level.

There's no Vercel platform feature for monitoring or managing llms.txt files. Even at the framework level, Next.js has no built-in convention for generating `llms.txt` the way it does for `sitemap.xml` or `robots.txt`, despite [community demand for one](https://github.com/vercel/next.js/discussions/81182) and an [open PR](https://github.com/vercel/next.js/pull/90580) to add it. Including checks for truncation analysis or freshness monitoring would highlight areas where Vercel has nothing to offer.

## Vercel's second article contradicts their own spec

The Haines article positions itself as an implementation companion to the Jordan article, linking to it three times. But the article contradicts the spec it claims to implement.

### Returning 200 for missing pages may be actively harmful

The Haines article recommends returning HTTP 200 with markdown suggestions when an agent requests a page that doesn't exist: "Return a 200 status, not 404. Agents need content they can act on."

My spec says the opposite, for empirically grounded reasons. In my observed testing, soft 404s (200 responses with error content) were *worse* than real 404s for agents. When an agent got a 200, it treated the response as the page it requested. It tried to extract documentation from whatever content is there. A clean 404 told the agent the page doesn't exist and it should try a different approach.

Returning 200 with "markdown-formatted suggestions listing the closest matches" creates a scenario where the agent receives content that *looks* like a successful response but contains information about a different page. My observed testing reveals two possible issues with this approach. 

First, a summarization layer between the web fetch results and the foreground agent directing the activity may or may not surface alternative page recommendations. Second, if the suggestions are close-but-wrong matches (and with fuzzy matching, there's a strong possibility they will be), the agent *may* get information that there's an alternative link and *may* follow it as a separate request to a related-but-incorrect page. (Two maybes make a weak possibility when non-deterministic agents are involved.) If the agent *does* fetch the alternative page information, it will likely use that information as if it were the answer to its original question. The developer gets plausible-looking but wrong guidance with no signal that anything went sideways.

The Jordan article doesn't address 404 handling at all. So Vercel now has two articles with conflicting or omitted guidance on this topic, and neither matches my empirical observations from actual testing.

### Agent auto-detection mostly detects crawlers, not agents

The Haines article describes a "three-layer detection approach" for identifying AI agents and serving them markdown automatically:

1. "User-agent matching: Check against a maintained list of known AI agent strings (Claude, ChatGPT, GPTBot, Cursor, Copilot, and others)"
2. "Signature-Agent header: The RFC 9421 standard header, used by ChatGPT's agent"
3. "Heuristic fallback: If the request is missing the `sec-fetch-mode` header"

This sounds comprehensive. It isn't. I [tested what coding agents actually send](https://dacharycarey.com/2026/03/05/how-to-measure-agent-web-traffic/) when they fetch documentation, and the user-agent strings I documented in the previous section don't match what Vercel claims to detect. [Checkly's research](https://www.checklyhq.com/blog/state-of-ai-agent-content-negotation/) on seven coding agents confirmed the same finding: only OpenAI Codex and Gemini CLI send identifiable AI agent strings. (Although their results with Cursor showed a browser-like user agent, while mine showed `axios`. Curious about how their testing differed from mine.) The other five are invisible to user-agent detection.

When Vercel says they detect "Claude, ChatGPT, GPTBot," what they're actually detecting is:

- **GPTBot**: OpenAI's *training crawler*, not a coding agent
- **ClaudeBot**: Anthropic's *training crawler*, not Claude Code
- **ChatGPT-User**: The chat product's browsing feature, not a coding agent

This is the same conflation again. The user-agent layer catches crawlers and answer-engine bots. It doesn't catch the coding agents that developers actually use.

The RFC 9421 Signature-Agent header is used by ChatGPT's browser automation tool (the "Operator" successor), not by any coding agent. And the heuristic fallback (missing `sec-fetch-mode`) is the only layer that would actually catch coding agents, but it also catches RSS readers, link preview generators, health checks, and any other non-browser HTTP client. It's a valid technique for "serve markdown to anything that isn't a browser," but it's not agent detection. Calling it that overstates what it does.

The Haines article presents agent detection as a solved problem. It isn't. My research into agent visitor detection found that reliable detection requires combining multiple signals (asset-less page views, missing browser headers, JS beacon absence, honeypot links) into a confidence score. User-agent matching is the weakest signal for coding agents specifically.

## An aside: Vercel's own AI product doesn't use these mechanisms

I've gotta laugh at this one. Vercel's AI coding product, v0, does not rely on llms.txt or live documentation fetching for its documentation access.

In "[How We Made v0 an Effective Coding Agent](https://vercel.com/blog/how-we-made-v0-an-effective-coding-agent)" (Max Leiter; January 7, 2026), Vercel describes their approach: "Instead of relying on web search, we detect AI-related intent using embeddings and keyword matching. When a message is tagged as AI-related and relevant to the AI SDK, we inject knowledge into the prompt." They use "hand-curated directories with code samples designed for LLM consumption." The rationale: "You may get back old search results, like outdated blog posts and documentation. Further, many agents have a smaller model summarize the results of web search, which in turn becomes a bad game of telephone between the small model and parent model. The small model may hallucinate, misquote something, or omit important information".

Vercel's own AI product handles documentation access through curated prompt injection rather than the live fetching mechanisms their spec recommends for everyone else. That's not inherently contradictory (v0 has different constraints than a general-purpose coding agent), but it's worth noting that the company publishing an agent readability spec chose a different approach for their own product. It shows that Vercel was aware enough of these considerations that they chose to work around them instead of trying to handle all the web fetch nuances that all three pieces of content describe.

## What Vercel adds

The Jordan article *does* include things my spec doesn't:

- `robots.txt` configuration for AI bots
- `AGENTS.md` / skill files
- `sitemap.xml` and `sitemap.md`
- Schema.org / JSON-LD structured data
- OpenGraph tags
- OpenAPI/Swagger schema linking
- `<link rel="alternate" type="text/markdown">` header

Some of these are useful. The `<link rel="alternate">` header for markdown discovery is a good addition. AGENTS.md is an established format (though it was created by Sourcegraph, not Vercel, and is now [stewarded by the Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)).

The Schema.org and OpenGraph recommendations deserve a closer look. The Jordan article requires JSON-LD with `headline`, `description`, `url`, `dateModified`, and `BreadcrumbList`, plus `og:title`, `og:description`, `html lang`, and `link rel="canonical"`. A couple of these have plausible agent utility: `dateModified` could help an agent prefer fresher documentation, and `BreadcrumbList` could help it understand where a page sits in a doc hierarchy. But most of the list (`og:title`, `og:description`, `html lang`, `link rel="canonical"`, JSON-LD `headline` and `url`) duplicates information already available in the page's `<title>`, `<h1>`, and content, or serves search engine indexing and social media link previews. These are standard SEO practices. A coding agent fetching a specific documentation page during a development session doesn't need OpenGraph tags to understand what it's looking at.

Others are less grounded. `sitemap.md` is not referenced by any agent platform documentation I've found. The text-to-HTML ratio of 15% is an SEO metric that doesn't address how agents actually process pages (the real problem is truncation budgets and content-start-position, not a ratio). The claim that "AI agents respect robots.txt directives" is inaccurate for coding agents, as discussed above.

## How to evaluate platform-published recommendations

This pattern, where a platform publishes "best practices" shaped by its own constraints, is not unique to Vercel. I wrote about a [similar dynamic with Anthropic and the Agent Skills spec](https://dacharycarey.com/2026/03/20/why-platform-shouldnt-own-open-spec/). It's going to keep happening as more companies stake out positions in the agent ecosystem. Here's a checklist for evaluating any platform-published specification or recommendation:

**Does it cite empirical data, or just make assertions?** The Jordan article states "Sites optimized for agent readability get cited more often, surface in more answers, and reach wider audiences" without any supporting data. It states "AI agents respect robots.txt directives" without testing it. Claims without evidence should be treated as hypotheses, not recommendations.

If content *does* provide evidence, evaluate it yourself to make sure it actually demonstrates what the content claims. With AI tools in the content creation mix, it requires a lot of rigor to validate claims. A team working quickly may speedrun or skip entirely the claim validation step, so you may be reading bad recommendations based on misinterpreted data.

**What does it omit, and do those omissions align with the publisher's limitations?** Check whether the spec avoids topics that would be uncomfortable for the platform. Content-start-position is uncomfortable for a company whose framework produces 500KB HTML pages. Size limits are uncomfortable when your own properties fail them.

**Would the publisher's own products pass these checks?** Vercel's `llms.txt` is 336 KB, seven times the recommended threshold. Their Web Analytics can't see agent traffic. Their bot management, if enabled, catches coding agents alongside training crawlers. A spec that conveniently avoids checks its author would fail is telling you something about whose interests it serves.

**Is the audience clearly defined?** Conflating different user types (coding agents vs. training crawlers) allows a spec to recommend things that serve one audience (the one the platform monetizes) while claiming to serve another (the one documentation teams care about).

**Is there a version number, a changelog, and an open contribution model?** Neither Vercel article has any of these. They're KB articles that can change without notice. [My spec](https://agentdocsspec.com) is versioned (v0.2.1), has a changelog, accepts community contributions via [GitHub](https://github.com/agent-ecosystem/agent-docs-spec), and is licensed under CC BY 4.0.

## What I'd recommend

If you're a documentation team trying to make your site work better for coding agents, here's what I'd suggest:

**Start with the empirical research.** The [Agent-Friendly Documentation Spec](https://agentdocsspec.com) is grounded in observed agent behavior with measured thresholds. Run `npx afdocs check https://your-docs-site.com` and see where you stand.

**Don't ignore platform-published guidance entirely.** Vercel's content negotiation work is useful. Their `<link rel="alternate" type="text/markdown">` recommendation is a good addition. Take the parts that are backed by evidence and make sense for your context.

**But check the incentives.** When a $9.3B platform tells you how to structure your documentation for agents, ask who benefits from each recommendation. If a recommendation aligns with what their platform does well and omits what it does poorly, weight it accordingly.

**Test against your actual audience.** The agents your users are actually running (Claude Code, Cursor, Copilot) are the ones that matter. Measure what they see when they fetch your pages. Check whether your content fits within their truncation limits. Verify that your rendering strategy actually delivers content to HTTP clients that don't execute JavaScript. These are the things that determine whether an agent can use your docs, and they're the things that platform-published specs are least likely to tell you about when they'd make the platform look bad.
