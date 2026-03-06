---
title: How to Measure Agent Web Traffic
author: Dachary Carey
layout: post
description: In which I lurk in server logs and try to make sense of what I see.
date: 2026-03-05 21:00:00 -0500
url: /2026/03/05/how-to-measure-agent-web-traffic/
image: /images/how-to-measure-agent-web-traffic-hero.jpg
tags: [ai, documentation]
draft: false
---

After sharing my recent findings about how agents use docs with the folks at work, one of the first questions (from multiple people!) was: how do we measure the impact of these problems? Writ large, how do we know things like:

- How many agents are trying to access our docs and running into issues?
- How many docs pages are adversely impacted by these issues?
- How can we tie agent access issues back to developer/product outcomes?

These are questions with many facets, and we're still talking about what the answers might look like. But the first two problems seemed immediately tractable, so we can start digging into those things right away.

## How many agents are trying to access our docs?

### What does "agents trying to access our docs" mean?

Many people have told me about this stat from Mintlify: "50% of traffic is agents." This [Mintlify article](https://www.mintlify.com/blog/ai-traffic) goes into a little more detail, but there's not a lot of info about how they're measuring that. I get it - Mintlify is a platform company and this info is part of their secret sauce that they're trying to sell to customers. But without knowing how they're tracking this, exactly, it's hard to really dig into this statistic. The number seems high to me, and I'm wondering if they're lumping _all_ AI-related traffic into this bucket. So before I talk about my take on how to measure agent traffic, for this article, I'm thinking of AI traffic as three distinct buckets:

- Traffic from web crawlers collecting data to train models. I went into more detail about this in my recent article [LLMs vs. Agents as Docs Consumers](https://dacharycarey.com/2026/02/26/llms-vs-agents-as-docs-consumers/)
- Traffic related to AI answer engines. Things like ChatGPT and other AI answer engines have mechanisms to scan the web for fresh content to keep their user answers up-to-date. I haven't really talked about this as a distinct modality, but I *think* it's different than model training and agents
- Traffic related to agents trying to perform tasks on behalf of their users in-the-moment. Most of my focus in this area has been coding agents like Claude Code, GitHub Copilot, and Cursor, but they're not the only agents who may be accessing your docs in real time to help their users perform tasks.

From what I've seen so far, I would believe that if you lump all three of these buckets together as "AI traffic" - that would conceivably reach 50%. But I have a hard time thinking the third bucket, which is what I'm focused on here - agents trying to perform tasks on behalf of their users in-the-moment - is 50% of total traffic. This is totally my gut take on the issue based on a limited sample size of data - I *don't* have the type of cross-customer data that Mintlify has as a platform company, so I *can't* see the patterns they see - but I also understand the reality of a platform company that wants to sell you services is to emphasize the data that makes you want to buy those services.

I don't have a pony in that race. I'm just focused on trying to help documentation teams understand how agent access patterns might affect docs consumption, product understanding, and customer impact.

So with that out of the way - here's my take on how docs teams can start to measure how agents are trying to access docs.

### Measure traffic to agent-friendly content

The first and easiest option, in my opinion, is to measure the traffic to agent-friendly content. How many hits does your llms.txt get? If you already serve docs in markdown format, how many hits are you getting to those markdown pages?

My initial assumption was that this traffic would be nearly 100% agents. But after talking with our docs platform team and seeing some actual numbers, the picture is muddier than I expected. There are several ways non-agent traffic can end up on your markdown pages:

- Your llms.txt acts as a discovery mechanism for *all* crawlers, not just agents. Any training crawler or search engine bot that finds your llms.txt will follow those markdown links.
- Search engines are good at pattern discovery. If they find one `.md` URL, they may try the pattern on other known paths, even if those URLs aren't in your sitemap.
- If anyone links to your markdown pages (blog posts, READMEs, Stack Overflow answers), crawlers will follow those links.
- Some developers genuinely prefer reading raw markdown when they're quickly scanning for an API signature or copying a code example.
- If your server supports content negotiation and some clients send Accept headers requesting `text/markdown`, those requests might show up as markdown traffic even though the client didn't request an explicit `.md` path.

So markdown traffic is a *signal* for agent activity, not a *metric*. It's probably skewed heavily toward non-human consumers, but "nearly 100% agents" was too strong a claim. The proportion will depend on how discoverable your markdown pages are, whether your llms.txt is getting crawled, and how many external links point to markdown versions of your content.

That said, I still think it's one of the better proxies we have. Human users overwhelmingly browse the HTML versions of docs pages, so markdown traffic is at minimum a useful indicator that *something other than a typical human user* is accessing your content.

### Measure traffic for specific user agents?

When I started socializing this stuff at work, the lead of our docs platform team reached out to me to brainstorm what other types of things we might look at to measure traffic. Our DOP (Documentation Platform) team has already been tracking some forms of AI traffic using known user agent strings, plus some educated guesses.

If you're not familiar with [user agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/User-Agent), check out that Mozilla reference, but the tl;dr: is - it's an identifier that accompanies a request to access content on a website server. Web browsers typically identify themselves in some way through the user agent they pass in the request header. Historically, this related to getting content optimized to serve a given browser. But once people figured out that *some* browsers were getting "better" content than other browsers, they started sending the wrong user agent string *on purpose* to get the "better" content. Now user agent strings are commonly misused for a variety of reasons, so it's an imprecise identifier at best.

But sometimes, imprecise signal is better than entirely absent signal.

In my early spelunking with Claude, Claude did some preliminary research (reading content on the web), and found that agent platforms typically don't send an identifiable user agent. Unlike AI web crawlers for model training or answer engine purposes, which *sometimes* send an accurate user agent string, agent platforms are indistinguishable from human users.

I wanted to dig in and see how true that was. So I did some testing!

I'll try to write up a full article on the experiment itself, because as usual I got some surprises, but the tl;dr: for our purposes here is:

- I manually tested web traffic for three agent harnesses I have access to right now; Claude Code, GitHub Copilot (through VS Code), and Cursor
- I sent each of these agents to a website URL I own whose server logs I can see
- I asked them in a very neutral prompt to access the website and then read a specific page of the website (no mention of llms.txt or markdown pages)
- I observed what I could see of the web fetch behavior in the agent harness, asking questions to drill into specifics when I observed something unexpected
- I watched the server logs *while* agents were performing the access to see what was happening on the server

One of the tools, GitHub Copilot, sent a user agent string that looks like normal browser traffic at a glance:

```text
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Code/1.109.5 Chrome/142.0.7444.265 Electron/39.3.0 Safari/537.36
```

If you know what to look for, you can spot `Code/` and `Electron/` in the string, which identify it as VS Code rather than a regular browser. But if you're just scanning your server logs for obvious bot traffic, this blends right in with normal users.

It seemed to be using a browser-based fetch tool, because the request immediately triggered related requests for CSS and other assets that a browser would request. The agent doesn't *need* these assets, but browser engines request them automatically, so the secondary requests happen without the agent doing anything in particular to spawn them. It's just a function of how the GitHub Copilot `fetch_webpage` tool is implemented.

The request to view the second page on the site came consistently 15-20 seconds after the first request. I theorized that agent requests might come *far* too fast to be human requests, and so if an agent requested a second page within a couple seconds of the first, the second request coming from the same IP address and user string "too fast" to be a human might signal agent. But is ~20 seconds too fast to be a human? I don't actually know. So I don't think we can lean on this with confidence, although we might consider it a heuristic for "signal" instead of "metric."

For the other two agents, they used HTTP access libraries instead of a browser-based tool. It makes sense - the agent doesn't need the CSS assets and one could argue that GitHub Copilot's implementation is wasteful. Both Claude Code and Cursor used the *same* HTTP access library, although they were on different versions. If I'd had access to a bunch of metrics from yesterday, I'd look for those HTTP access libraries, track the versions to what I observed from each of these agents, and I could have told you how much traffic we were getting from either Claude Code or Cursor. But those versions will change, so that one-day snapshot isn't a stable state of the world.

I also observed a case where GitHub Copilot had some trouble accessing the site due to what it termed as "environmental variables and transient issues" - i.e. intermittent flakiness - so instead, it reached for a Python HTTP access library and got site data through the Python lib. So now that's two things to watch for GitHub Copilot - the browser-like user agent string, *or* a Python HTTP access library user agent. And then we have the question of - is this derived from model data, or hinted from the agent harness? If I used GPT 5.3 through some other agent platform, would it potentially do the same thing in the event of flakiness? I have no idea! So we can't necessarily assume that the Python HTTP access is a GitHub Copilot trick - maybe it's a general LLM workaround that could reflect *any* agent platform.

So when it comes to user agents, I don't think we can reliably correlate a specific user agent string with a specific agent platform. But there *are* patterns we can look for that are *probably* generically "agents" - and that may have to be good enough for now.

(Ugh, so many hedge words!)

## How many of these agents are running into issues?

So if we have a way to measure agent traffic, even if it's hand-wavey, the next question is: how many of these agents are running into issues?

This is where things get tricky. I know from [my Web Fetch spelunking](https://dacharycarey.com/2026/02/19/agent-web-fetch-spelunking/) that truncation limits vary by platform. Claude Code truncates at around 100KB of text. Other platforms presumably have limits too, but most won't say what they are. If we could reliably tie particular user agent strings to particular platforms, we could look at how many visits we're getting from each, correlate those with known truncation limits, and start to triangulate the proportion of agents that are probably running into issues on a given page.

But as I covered above, tying user agent strings to specific platforms is shaky at best. So instead of trying to identify *which* platform is hitting you and whether *that* platform's truncation limit is a problem, I think a more practical approach is to work backwards from your content.

You know your page sizes. You can measure the character count of each page as serialized HTML, and if you serve markdown, as markdown too. You know from the limited data that *is* available that truncation limits tend to fall somewhere in the 5K-150K character range depending on the platform and tool implementation. So if a page is 300K characters of HTML and 80K as markdown, you can be pretty confident that *most* agents accessing the HTML version are getting truncated content, and some agents accessing the markdown version might be too (depending on their particular limits).

This is an imperfect heuristic. But it lets you identify pages that are *likely* causing problems without needing to solve the much harder problem of attributing traffic to specific platforms. If a page is 20K characters of clean markdown, it's probably fine for almost any agent. If it's 400K characters of HTML with inline CSS, it's probably broken for *every* agent. You don't need to know which agent is which to know that.

## How many docs pages are adversely impacted by these issues?

You can approach this from two directions.

**Slice by traffic.** If you're serving markdown versions of your pages, you can look at which pages are getting the most markdown hits. Those are probably your most non-human-accessed pages, and they're the ones worth auditing first. If a page is getting a lot of agent traffic *and* it's over 100K characters, that's a page where agents are almost certainly getting incomplete content.

**Slice by content.** Alternatively, you can look at your docs holistically and ask: how many of our pages exceed reasonable truncation limits? This is the approach I'd recommend for an initial audit. Don't do it manually. Write a script that crawls your docs pages and measures the character count of each one, both as HTML and as markdown if you serve it. You'll probably find a distribution: a bunch of pages that are fine, and a long tail of pages that are way too big.

If you're using [afdocs](https://www.npmjs.com/package/afdocs), the page size check already flags pages that exceed configurable thresholds. Point it at your docs and you'll get a list of pages sorted by risk. It's a starting point, not the final word, but it gets you from "we have no idea" to "here are the 50 pages most likely to cause problems" in a few minutes.

And if you don't serve markdown at all? Then the honest answer is that 100% of your docs pages are potentially adversely impacted by these issues, because HTML pages carry all that CSS and JavaScript overhead that eats into the truncation budget before the agent ever sees your content. Even if you *do* serve markdown, individual pages might still be too long, or have structural issues like flattened tabbed content that makes the serialized version much larger than what a human sees on the rendered page. Serving markdown is necessary but not sufficient.

## Measurement -> Impact?

Everything I've talked about so far is measurement. But measurement doesn't equal impact. Knowing that 40% of your docs pages exceed agent truncation limits is useful information, but it doesn't tell you whether anyone *cares* about those pages. Impact is about the downstream consequences: failed tasks, abandoned signups, confused developers, lost revenue.

This is the hardest part to measure, and honestly, I don't think anyone has a great answer yet. The failure mode is largely invisible. A developer's agent silently gets truncated content, produces a wrong answer, and the developer either figures it out themselves (wasting time), or doesn't figure it out and ships something broken, or gives up and switches to a competitor with better docs. None of those outcomes are easy to trace back to "the agent couldn't read your docs page."

But you can make some strategic decisions even without perfect attribution. Start by identifying which pages matter most to your business. Your quickstart guide, your authentication docs, your billing API reference, your migration guide: these are the pages where a bad agent experience is most likely to cost you a customer. If those pages are over truncation limits, that's where you should focus your remediation efforts first.

The remediation work itself isn't trivial. Breaking up long pages into shorter ones that fit within truncation limits, restructuring tabbed content so the most common use case comes first in source order, adding markdown versions of pages that don't have them yet: all of this takes time and effort. If you can only fix some of your pages, fix the ones that matter most for your business. A quickstart guide that agents can actually read is worth more than a niche reference page that gets 10 hits a month.

I also think there's an opportunity here for docs platforms and analytics tools to start connecting the dots. If you could correlate agent traffic patterns with developer journey metrics (signup completion, first API call, time to "hello world"), you'd start to see the shape of the impact. We're not there yet. But the measurement work I've described in this article is the foundation you need to build on when those tools eventually exist.
