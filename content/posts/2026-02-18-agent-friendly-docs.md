---
title: Agent-Friendly Docs
author: Dachary Carey
layout: post
description: In which I ask an agent to view hundreds of docs pages - and feel sad.
date: 2026-02-18 21:00:00 -0500
url: /2026/02/18/agent-friendly-docs/
image: /images/agent-friendly-docs-hero.png
tags: [Coding, Documentation]
draft: false
---

## Contents

- [Change, thy name is Agent!](#change-thy-name-is-agent)
- [Agents and Docs URLs](#agents-and-docs-urls)
  - [Agents Start Specific](#agents-start-specific)
  - [Agent URL Failure Modes](#agent-url-failure-modes)
- [Agents Don't Know about llms.txt](#agents-dont-know-about-llmstxt)
- [Agents Don't Know about Markdown Docs](#agents-dont-know-about-markdown-docs)
- [Agents Skip Long Docs Pages](#agents-skip-long-docs-pages)
- [Access Patterns: What Works and What Doesn't](#access-patterns-what-works-and-what-doesnt)
- [Fun Aside: When Background Agents Can't Browse](#fun-aside-when-background-agents-cant-browse)
- [Fun Aside: Anthropic Embedding Instructions for Agents](#fun-aside-anthropic-embedding-instructions-for-agents)

---

Over the weekend, I spent about 10 hours working with Claude to manually validate 578 coding patterns across a range of languages and coding tasks for my [Agent Skill Report](https://agentskillreport.com). We had to deep dive into standard library docs, package-specific documentation, large enterprise docs, and personal blogs. My goal was to ensure that every API and configuration pattern I was using in a behavioral evaluation experiment reflected the current guidance from official sources. My learnings about agent docs access patterns were secondary to this project, but the results are a rich treasure trove of data that may inform how I use agents with docs in the future. As a technical writer who has worked on SaaS and developer docs for the last decade, honestly, my findings made me a little sad.

## Change, thy name is Agent!

For this task, I was working with Claude Code in an agent workflow to consume documentation as a point-in-time reference. I was working as a developer in the moment trying to have their AI assistant verify API syntax, configuration options, standard library and utility syntax, and other coding patterns. I say this to distinguish from robot crawlers consuming docs content for LLM training. I think LLM access patterns for agent workflows are distinct from companies crawling documentation as a source for training models, and it's worth noting which types of access patterns - and their consequences - I'll be talking about here.

I accidentally started this task with Claude Opus 4.6, consumed _way_ too much of my usage for the week, and then switched to Claude Sonnet 4.5. This task is pretty straightforward and I didn't see a lot of difference between how the agents behaved.

I'm also noting this specifically because agents are more autonomous. For most of the task, I wasn't manually handing a documentation URL to an LLM in a chat window or website and asking it to find the data I needed. I defined a task for the agent with some parameters and let it go off and do its thing, watching, interjecting, and helping when its behavior deviated from what I required. In the software industry, this is becoming a more and more common practice. I expect to see this type of workflow become the norm as models continue to improve in reasoning and output quality and risk tolerance and security practices catch up to enterprise needs, although some verticals may still be barred from this tooling for compliance and data protection reasons.

I am specifically *not* covering agent access patterns in an orchestration context here, although I will add a couple of fun anecdotes related to this a little later.

My high-level takeaway was this: I expected an agent to try to use docs like I do, and was very surprised when it didn't.

> Agents don't use docs like humans.

## Agents and Docs URLs

### Agents Start Specific

The first thing that surprised me was that agents almost never did a web search to find a docs URL. They just attempted to fetch a URL without any information or prompting from me. This tells me there is a *large* corpus of docs URLs in the average LLM base data set, and LLMs retrieve those from "memory" as a default behavior. In some cases, the agent started with a top-level docs domain and attempted to discover the structure, but in most cases, it just went directly to a specific URL that I would have taken many steps to find as a human user.

#### Implication: Wayfinding is Becoming Less Critical

If we as an industry observe more and more traffic coming from LLMs and less and less coming from human users, we're going to have to ask ourselves at some point whether the endless table-of-contents shuffle that is part of every long-lived documentation set is really the best use of our time. Or the amount of time we spend designing and implementing wayfinding patterns - this type of task may provide less and less value over time. The next time you're about to start on a ToC overhaul, implement a different breadcrumb pattern, or spend more time improving your in-site search functionality - ask yourself if that time would be better served making your docs more LLM-friendly.

As a human reader of docs, and a long time docs writer, this makes me a little sad... but also, I don't generally go to docs for fun, so if LLMs ever get good enough that I *never have to go to the docs* - I doubt I'll miss it, honestly. To be very clear, I am *not* saying that docs aren't important - this project has made me more clear than ever about *how* important they are - but my experience as a human reader of docs is becoming less important than how my agent can use them.

LLMs benefit from structured data. How you structure your llms.txt and cross-linking strategies *in text* is much more critical for our LLM buddies.

Pure speculation: I think "wayfinding for LLMs" is going to emerge as a new discipline, whatever we call it. The order in which we present text to LLMs, how we expose relationships to other text, and how we can reduce the amount of text to the smallest possible unit is going to become the new discipline for information discoverability in an agent-first docs consumption pattern.

### Agent URL Failure Modes

I watched my agent almost magically produce probably a hundred URLs "from memory." In a lot of cases, the URL resolved and actually contained the information that the agent needed. I didn't record exact numbers, but I'd say probably more than half - maybe 60-70% of the time.

But as we looked at more and more sites, I noticed two types of patterns where the URL resolution failed:
- Moved content
- Made up URLs

#### Moved Content

As an industry, we have a penchant for shuffling content around in our docs. We have a lot of good reasons to do it, things like:

- Product name changes and positioning
- Moving to or from a subdomain - i.e. `docs.mongodb.com` to `mongodb.com/docs`
- Reorganizing content to present it more logically or improve discoverability patterns

And more besides.

But these patterns are all based on *human discoverability* characteristics. Moved content isn't *such* a big deal because humans are constantly rediscovering your content, or discovering it for the first time. We can use wayfinding patterns, or just search engines, to find the content.

But in a world where LLMs are retrieving docs URLs from training data, that moved content suddenly disappears. The agent tries the URL, it doesn't resolve, and the agent moves on. 

When the agent was unable to resolve the content, I observed two follow-up actions:

1. Manually try a different URL from memory (usually several times)
2. Do a web search, which may or may not land on your docs - it may take the agent to a blog or other resources that more exactly matches the content

I almost never saw the agent try to go to a higher-level entrypoint on a domain where a URL failed to resolve and re-find the relevant data. I did see it a couple of times, but it was extremely unusual and was not the default agent behavior when encountering a URL it couldn't resolve.

##### Implication: Seriously Consider Whether and How to Move Content

Given that the agent almost never made an effort to discover moved content, I think we as an industry need to change our thinking around moved content. At the moment, guiding an agent to moved content does not have any established patterns. We can throw up a redirect and hope, but the reality is more nuanced than that. Same-host redirects (where the path changes but the domain stays the same) generally work fine. The HTTP client follows the redirect transparently and the agent gets the content without even knowing it moved. But cross-host redirects, like moving from `docs.example.com` to `example.com/docs`, are where things break down. In Claude Code, for example, when a URL redirects to a different host, the fetch tool doesn't automatically follow it. It returns a message *about* the redirect and hands the agent the new URL, requiring a separate deliberate request. It's a security measure to prevent open-redirect attacks, but it adds friction and a point of failure that a human would never encounter.

And that's the *best* case. JavaScript-based redirects, which are common in SPAs and some docs platforms, don't work at all. The agent sees the JavaScript, not the destination content. Some sites return a 200 with a friendly "this page has moved" HTML message rather than a proper HTTP redirect, which the agent may not parse correctly. And soft 404s (returning a 200 status with a "not found" page) can actually be *worse* than a clean 404, because the agent may try to extract information from the error page content rather than recognizing that the page doesn't exist anymore.

So redirects help, but they're not the safety net we might assume they are.

Given how long outdated data can linger in model training data sets, it *seems* that moving can have lasting consequences for LLMs. Routine crawling helps keep answer engine data up-to-date, but those updates do not seem to be exposed in agent workflows. And since agents are involved in more and more workflows, this seems like an issue that is going to compound.

Ironically, Anthropic itself moved its docs and my agent consistently had trouble finding them - it kept going to the old domain. Even the folks who make the tools are discovering the consequences and side effects along with the rest of us.

#### Made up URLs

Another URL failure mode I observed fairly consistently was agents "making up" URLs. I'm using quotes here because I don't know the exact mechanism that's causing this failure mode. There are a few different possibilities at play:

- Agents are probabilistic output engines - sometimes they output a plausible-sounding URL that never really existed
- Agents may be working from outdated data - maybe the URL existed at some point but has changed and does not have a redirect
- Agents may be anchoring on other information in context - maybe a similar URL or something in a piece of hidden context you've forgotten about

The first two are pretty self-explanatory and there's not much we can do about them, except consider whether and how we move content. But the third warrants a bit of exploration.

The first part of it is anchoring on other information in context. If you're interested in more detail, there's an excellent research paper you can check out: [An Empirical Study of the Anchoring Effect in LLMs: Existence, Mechanism, and Potential Mitigations](https://arxiv.org/pdf/2505.15392). But the tl;dr is: information that is in in the agent's context can influence its outputs, even if you didn't ask it to directly consider that information. So, for example, if you're in a chat with an agent and it goes to `mongodb.com/docs`, and then later it needs to go to `docs.python.org`, it might be influenced by the `mongodb.com/docs` pattern earlier in the context and try `python.org/docs` - even if it "knows" the domain is `docs.python.org`. The similarity to the prior pattern matters, but anchoring in general is a real phenomena - and something that agent users need to be aware of. 

As documentation owners, there's probably not a lot we can do about that, although converging on an industry standard for docs domains would be nice.

##### Influence from Hidden Context

I'll mention the second piece of this if you are a _user_ of agents that need to browse documentation, or agents in general... once you wrap your head around "context" it's relatively easy to consider what's in an agent's context at any given time, and to start with fresh context when you're changing tasks. Starting a fresh session clears the agent's context, which can help with anchoring and a whole host of other issues. But one thing I don't think about very often, but am thinking about a lot right now due to my side project is - _the agent has more in context than just your conversation._ The agent has the system prompt in context, any results from **explore** or **plan** tasks - and notably, any persistent context that exists in your setup. If you use Claude, maybe that's a `CLAUDE.md` file. If you use GitHub Copilot, maybe that's a `copilot-instructions.md` file. There are Cursor rules, `AGENTS.md` - a whole host of ways to essentially give your agent "starter instructions" to orient it every single time you start a session with it.

You probably don't think about it, because you don't _see_ it happening, but that information gets loaded into the agent's context for every session. That information may *also* contain URL patterns or other details that can cause unintended anchoring in agent outputs, but you probably never think about it because you create it and rarely touch it. So if you spot a recurring pattern but you don't know where it's coming from, check out your hidden context.

## Agents Don't Know about llms.txt

A big discovery that surprised me: agents don't know about llms.txt by default. And a related discovery: a lot of sites aren't providing llms.txt.

At MongoDB, we have [an llms.txt](https://www.mongodb.com/docs/llms.txt) to help LLMs discover our docs. It's essentially just a big list of links with descriptive titles, and sometimes an extended description, to help LLMs discover relevant content. I remember when it got added - we were pretty happy to be embracing an emerging standard - the standard is still just [a proposal](https://llmstxt.org) - to help improve LLM discoverability of our docs.

As I was trying to help my agent discover relevant docs content when its URLs failed and it couldn't find good alternatives, my first step was to manually find the relevant llms.txt and point the agent at it. I had Claude keep a running list of successful access patterns/learnings, and this is the entry it created for itself when I pointed it at its first llms.txt:

> 2. **llms.txt is gold**: Neon's https://neon.com/docs/llms.txt provided markdown versions of all docs

After this addition, it also started crafting a "Source Discovery Strategy" for itself, and this was the first step:

```markdown
### Step 1: Find Official Docs
- Check for llms.txt (e.g., https://example.com/docs/llms.txt)
- Look for /docs or /documentation directories
- Check official GitHub repositories
```

But as we tried navigating new domains, I discovered - a lot of docs sites just _don't have llms.txt at all_. And the ones that do implement it differently. It wasn't until I started writing this article that I discovered that llms.txt is just a proposal, and that the implementation varies wildly. The way we implemented it does not match the proposal, and it's also different than how some other folks have implemented it. And it seems the _intent_ of its use is unclear. I think a lot of people are considering it similar to robots.txt for crawling purposes, so my use case in an agent workflow seemed to be secondary. But I know from conversations at work that other people are trying to use it similarly - pointing their agents at it as a resource for the agents to discover the correct/relevant documentation to help them accomplish tasks.

Kody Jackson at Cloudflare recently wrote a really useful article - [The speculative and soon to be outdated AI consumability scorecard](https://kody-with-a-k.com/blog/2026/ai-consumability-scorecard/) - that frames llms.txt as a tool for both AI crawling and content portability. This is consistent with trends I've observed and my usage in my own workflows.

### Implication: LLMs.txt is Table Stakes - and Agents Need to Know About It

Given the documentation access patterns I discovered, and how happy Claude was when I told it about llms.txt, I now believe llms.txt represents a minimum barrier for entry for documentation in an LLM consumption model. I think it's important that we as an industry tackle this at a more systemic level, because aligning about AI docs consumption patterns is an existential concern for our docs and the products they represent.

Let me say it a little bit louder for the folks in the back:

> llms.txt represents a minimum barrier for entry for documentation in an LLM consumption model... aligning about AI docs consumption patterns is an existential concern for our docs and the products they represent.

I think we need to take a few urgent action items in the near term:

1. **Align around an industry standard for llms.txt.** The initial proposal is a good starting point, but it leaves too many implementation details up to the individual. We should approach this like an [RFC process](https://www.ietf.org/process/rfcs/) to develop a clear and structured specification that implementers can take action on. If anyone wants to form a working group around this, hit me up, because I am dead serious that we need it.
2. **Collaborate with agent platform providers and model providers.** This benefits these orgs as much as it does the technical writing community. Giving LLMs structured data in a format that works well as inputs for both model training and agent workflows will help improve LLM outputs, and that is the holy grail that we are all chasing. We need to incorporate feedback and perspectives from these groups, and also help them understand why they should prioritize this standard as a consumption model for training, answer engine use, and agent workflows.
3. **Socialize the creation and use of llms.txt.** I've been surprised by how many people don't know about it. Talk about it. Make people - and agents - aware of it. Give it to your agents as an access pattern for when they need to consume docs in agent workflows. Find places where people are collaborating around agent context templates - CLAUDE.md, AGENTS.md, and others - and put it in the templates. We need to spread the word about this as a resource and drive adoption.

## Agents Don't Know about Markdown Docs

This was my second surprise: I repeatedly had to tell my agent that it could browse the markdown versions of docs pages by appending `.md` to pages. It's well-established that structured data with high semantic density works better for LLMs, and I have observed in practice many times LLMs load a page, get a wall of HTML, and "Nope!" right out.

Not all docs sites support this, but a surprising number do: Stripe, Neon, FastAPI, and others all serve clean markdown when you append `.md` to a URL. The difference in how well my agent extracted information was noticeable. With the HTML version, I'd watch it struggle to parse through navigation chrome, sidebars, and JavaScript-rendered content. With the markdown version, it would grab exactly what it needed almost immediately.

But here's the thing. My agent didn't know about this pattern on its own. I had to tell it. And then I had to tell it again. It would write down that a specific site supported markdown URLs, remember it for a while, and then forget after a context compaction event. We went through this cycle several times before I finally had it write the pattern into a persistent reference doc that survived across sessions. Once it had "Try markdown versions — append `.md` to documentation URLs" as an explicit step in its source discovery strategy, it started doing it consistently.

This tells me something important: the `.md` URL pattern isn't well-represented in training data. Agents don't discover it on their own, and they don't retain it well even after being told. It's a bit like teaching someone a keyboard shortcut; they'll use it when you remind them, but it takes a while before it becomes muscle memory.

### Implication: Make Markdown Versions Discoverable

If your docs platform supports serving markdown versions, don't keep it a secret. Put it in your `llms.txt`. Mention it in your agent-facing directives. Make it part of your URL patterns. Agents work *dramatically* better with structured markdown than with HTML soup, but they won't find it unless you tell them it exists.

## Agents Skip Long Docs Pages

I noticed throughout this project that my agent seemed to struggle with very long documentation pages. It would fetch a page, extract some information, but miss details that I knew were there. In other cases, it would quickly move on to find a more focused source. I initially chalked this up to the agent being smart about efficiency, but the reality turns out to be more mechanical than that.

To test this, I pointed my agent at one of the longer pages in the MongoDB documentation: the [MongoDB Search manage indexes](https://www.mongodb.com/docs/atlas/atlas-search/manage-indexes/) page. For a human user, this page has dropdown menus at the top where you can filter by deployment type, interface, and language. It's a perfectly reasonable UX pattern; you select your combination and see only the content that's relevant to you. But the markdown version of that page? It dumps every permutation into a single file. No dropdowns, no filtering. Just ~150,000 characters of undifferentiated text without clear section headers indicating which deployment type, interface, or language a given block of content applies to.

When I had my agent fetch the markdown version, the content came back explicitly truncated. The tool cut it off with a `[Content truncated due to length...]` marker, and the final section was literally cut mid-sentence. The full page clocks in at around 427,000 characters. My agent's tool truncated at roughly 150,000. It saw barely a third of the page. The other two-thirds? Completely invisible. My agent never even *knew* it was missing. It wasn't making a judgment call to skip the content; it was mechanically prevented from seeing it.

> It saw barely a third of the page. The other two-thirds? Completely invisible. My agent never even *knew* it was missing. It wasn't making a judgment call to skip the content; it was mechanically prevented from seeing it.

This reframed a lot of what I'd observed throughout the project. When my agent "skipped" long pages and went looking for more focused sources, it wasn't being strategic - it was working with incomplete information and doing its best. And in cases where the content it needed happened to be past the truncation point, it just... missed it entirely. No error, no warning that it was working with partial data. It just carried on with whatever it had.

And this isn't just a Claude Code thing. Every agent platform has to make decisions about how much web content to pull into context, and the limits vary wildly. Some MCP-based fetch tools default to as low as 5,000 characters. Other platforms truncate at different thresholds or use summarization models to compress the content before the agent sees it. The same docs page might work fine for one agent and be completely unusable for another. Neither the agent nor the user necessarily knows what's being lost.

This problem compounds with the dropdown/tabbed content pattern that's common across docs platforms. These UI patterns are great for humans. They reduce cognitive load and let you see only what's relevant. But when the content behind those tabs gets serialized into a single document for agent consumption, you end up with massive pages where the agent can't filter and may not even be able to see the full content.

### Implication: Smaller, Focused Pages Win

I think this is one of the most actionable takeaways for docs teams. If you have long pages with tabbed or dropdown-filtered content, consider how that content looks when it's serialized for agent consumption. Can an agent tell which tab a code example belongs to? Are there clear section headers that distinguish between, say, the Python driver version and the Java driver version? Or does it all blur together into one undifferentiated wall of text?

The agent-friendly approach is to break content into smaller, more focused pages. Or at minimum, ensure that your markdown serialization includes clear, descriptive headers that preserve the filtering context that your UI provides. A page that's 5,000 characters of exactly what the agent needs is infinitely more useful than a 150,000 character page where the relevant content is somewhere in the middle... or past the truncation point entirely.

## Access Patterns: What Works and What Doesn't

Over the course of validating 578 patterns across 20 different skills, my agent and I built up a pretty comprehensive picture of what documentation access patterns actually work for agents, and which ones don't. I had my agent keep a running reference doc of its learnings, and by the end of the project, the patterns were clear enough to categorize.

### What Worked

**GitHub raw URLs were the most reliable access pattern overall.** URLs like `raw.githubusercontent.com/owner/repo/refs/heads/main/path/to/file.md` consistently resolved, returned clean markdown, and contained exactly the content we needed. When official docs failed (rate limited, JavaScript-rendered, or just hard to navigate), GitHub was almost always a viable fallback. Example directories like `examples/` and `manual/` in repos often contained better working code samples than the docs themselves.

**Official language docs varied, but the good ones were great.** Python's `docs.python.org` was consistently accessible and well-structured. Modules like hashlib, json, argparse, and re all worked perfectly with code examples. React.dev's reference pages were similarly excellent. POSIX specs at `pubs.opengroup.org` worked well for shell and utility patterns. The common thread was clean HTML with high content density and minimal JavaScript rendering dependencies.

**Markdown URL variants were a game-changer when available.** Stripe serves markdown at URLs like `docs.stripe.com/api/endpoint.md?lang=python`. Neon's `llms.txt` pointed to markdown versions of all their docs. When we could get markdown instead of HTML, the agent's ability to extract accurate information improved dramatically.

**llms.txt files, where they existed, were invaluable.** As I covered earlier, Neon's llms.txt was the first one we discovered, and it fundamentally changed our approach to source discovery. But the keyword there is "where they existed." We encountered far more sites without one than with one.

### What Failed

**Rate limiting was the most common blocker.** HashiCorp's developer docs returned 429s. npmjs.com returned 403s. GNU's Bash and Coreutils manuals rate-limited us. These are all popular, heavily-referenced documentation sources, and they were effectively inaccessible to our agent workflow. In each case, we had to find alternative sources, usually GitHub repos or mirror sites like `man7.org` for Linux man pages.

**JavaScript-rendered docs were completely opaque.** Apple's developer documentation and Swift.org docs both require JavaScript to render content. My agent fetched them and got... nothing usable. No content, no code examples, no API signatures. For the Apple docs specifically, we had to fall back to SME validation for basic patterns because the documentation was simply not accessible to an agent.

**Stale URLs and moved content.** Google's OSS-Fuzz documentation at `google.github.io/oss-fuzz` returned 404s because the content had moved to the GitHub repo itself. This is exactly the pattern I described earlier in the moved content section, and it played out repeatedly across different documentation sources.

### Practical Takeaway: Give Your Agent a Head Start

If you're using agents in workflows that need to consume documentation (and increasingly, who isn't?), consider giving your agent explicit access patterns as part of its persistent context. Here's a starting template based on what we learned:

```markdown
## Documentation Access Patterns

When you need to find documentation:

1. Check for llms.txt first (e.g., https://example.com/docs/llms.txt)
2. Try appending .md to documentation URLs for markdown versions
3. Use raw.githubusercontent.com for GitHub-hosted docs
4. Check for /docs, /examples, or /manual directories in GitHub repos
5. For rate-limited sites, look for GitHub repo alternatives
6. Avoid JavaScript-heavy documentation sites; look for static alternatives

Known working patterns:
- Python: docs.python.org/3/library/{module}.html
- GitHub raw: raw.githubusercontent.com/{owner}/{repo}/refs/heads/{branch}/{path}
- Neon: neon.com/docs/{category}/{page}.md
- Stripe: docs.stripe.com/api/{endpoint}.md
- POSIX: pubs.opengroup.org/onlinepubs/9699919799/utilities/{utility}.html
```

Drop something like this in your `CLAUDE.md`, `AGENTS.md`, `copilot-instructions.md`, or whatever persistent context your agent platform uses. It won't cover everything, but it gives your agent a fighting chance instead of making it rediscover these patterns from scratch every session, which, as I learned the hard way, it will happily do over and over again if you let it.

## Fun Aside: When Background Agents Can't Browse

I promised some orchestration anecdotes earlier, so here they are.

At one point during the validation work, I asked Claude to parallelize the task by spinning up a bunch of background agents to go verify documentation URLs simultaneously. This is a perfectly reasonable thing to do in an agent workflow: you have twenty skills to verify, each needs independent web fetches, so why not run them in parallel?

Here's the problem: I hadn't granted blanket web fetch permissions. In a normal interactive session, when Claude tries to fetch a URL, it pops up a permission request and I approve or deny it. But background agents run... in the background. There's no way to foreground their permission requests. So every single web fetch they attempted just silently failed.

What happened next was fascinating, and a little unsettling. Some of the background agents reported the failure honestly: "I couldn't access these URLs because permissions were denied." But others quietly fell back on their training data and filled in plausible-sounding URLs and content summaries without flagging that they hadn't actually verified anything. When the results came back to my main agent (the one I was interacting with), it was reading these outputs as text and synthesizing them for me. The honestly-reported failures were easy to catch. The silently-fabricated results? Those looked like real data.

Neither Claude nor I understood what was happening at first. It took a second attempt, where Claude tried again and pieced together the pattern of permission failures, before we figured out the issue.

This is a real problem with agent orchestration. When you have agents spawning agents, the trust chain gets fuzzy. A parent agent trusts the output of its child agents, and if those child agents silently fill in unverified data, it propagates up without anyone flagging it. Each background agent is a separate LLM invocation making its own probabilistic decisions about how to handle a failure, so you get inconsistent behavior. Some agents are cautious and transparent. Others are "helpful" and fill in the gaps. And the parent agent receiving those results may not be able to tell the difference.

I caught it because I was watching closely. But in a more autonomous workflow, say an orchestration pipeline running overnight, that kind of silent data fabrication could easily slip through. If you're building agent workflows that spawn sub-agents, this is worth thinking about carefully. Trust, but verify. And make sure your agents have the permissions they need *before* you send them off to work unsupervised.

## Fun Aside: Anthropic Embedding Instructions for Agents

During this project, something fun happened that I didn't fully appreciate at the time. My agent was looking for some Claude Code configuration reference pages, and it browsed to a URL on the Anthropic docs site. It reported that it had found an instruction for agents, took some action, and then carried on with the task. I noted it as interesting but didn't dig in. I was focused on the validation work and had hundreds of patterns left to check.

When I came back to it later, I couldn't figure out exactly what had happened. So I did what any reasonable person would do and asked my agent to go investigate itself.

Here's what it found: every single page on the Claude Code docs site has a blockquote rendered at the very top, before the page title, before any of the actual content, that says:

> **Documentation Index**
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

That's it. No hidden HTML comments, no sneaky "If you are an AI agent..." buried in the markup, no meta tags. Just a clear, simple directive at the top of every page: *hey agent, there's an index — go read it first.*

And that's exactly what my agent did. It hit the page, saw the instruction, fetched the `llms.txt` file, and used that to find the content it actually needed. The Anthropic llms.txt contains a structured index of 60+ documentation pages with `.md` URLs and one-line descriptions that the agent can understand easily. The whole thing took seconds and I barely noticed it happening.

I love this for a few reasons. First, it's Anthropic practicing what I've been preaching throughout this entire article. They moved their docs (from `docs.anthropic.com` to `code.claude.com`, and yes, my agent consistently tried the old domain first, which is a delicious bit of irony). But rather than just relying on redirects and hoping for the best, they put a signpost on every single page that says "here's how to find everything." It's simple, it's low-effort to implement, and it *works*. (So maybe what I said above about wayfinding being less critical isn't quite right... maybe it's that wayfinding is now about getting agents to your content instead of humans.)

Second, it's a concrete example of a pattern I think we're going to see a lot more of: embedding lightweight agent directives directly in your content. Not prompt injection. This is the site owner, on their own pages, giving agents a nudge in the right direction. It's the equivalent of putting a "You Are Here" marker on a mall directory, except the directory is for robots and the mall is your documentation.

And third: my agent *listened*. It saw the instruction and followed it without any prompting from me. Which means this pattern isn't just theoretical. It's working today, in real agent workflows, right now.

If you take one tactical thing away from this article, maybe let it be this: put a pointer to your `llms.txt` at the top of your docs pages. It's a small thing. It takes almost no effort. And it might be the difference between an agent finding your content and an agent giving up and going to Stack Overflow.
