---
title: Filtering AI News
author: Dachary Carey
layout: post
description: In which I build a system to filter AI-related content.
date: 2026-03-24 23:00:00 -0500
url: /2026/03/24/filtering-ai-news/
image: /images/filtering-ai-news-hero.jpg
tags: [ai, coding]
draft: false
---

When I decided I might be doing more than just digging into AI a little, I started to think about how I might want to learn about AI in a more realistic way. There were two facets to the problem: how do I find out about interesting AI-related developments, and how do I get more practical exposure to different AI workflows?

On the discovery front, the AI firehose is massive. So many companies are doing things in this space right now that there is a plethora of news every day. Most of it is marketing hype, but some of the marketing hype is actually signal. And there's also a lot of research, but the motivation behind the research influences how useful it is to me. So I needed a way to filter out low-value noise from high-signal content, and a way for me to assess what I actually want to look at. If there are 10, or 30, high-value content pieces every day, how do I make time to look at any of it on top of my full time day job and my second full-time research job?

So I decided to set up some system to discover content that I might find useful.

## Step 1: Pick some inputs

I decided I wanted to define a config file with inputs I might want to use for AI-related news sources. That way, if I want to add new items to my input sources, I could tweak a config file. After some discussion with my trusty pal, Claude, we decided to start with this initial config file:

```yaml
rss:
  - name: "Simon Willison"
    url: "https://simonwillison.net/atom/everything/"
  - name: "Dachary Carey"
    url: "https://dacharycarey.com/index.xml"
  - name: "Hacker News (AI agents)"
    url: "https://hnrss.org/newest?q=coding+agent+OR+LLM+code+OR+claude+code+OR+cursor+OR+aider+OR+copilot+agent+OR+codex+OR+vibe+coding"

arxiv:
  queries:
    - "cat:cs.SE AND (LLM OR \"large language model\" OR \"coding agent\" OR \"code generation\" OR \"code repair\" OR \"automated programming\")"
    - "cat:cs.AI AND (\"coding agent\" OR \"software engineering agent\" OR \"code generation agent\" OR \"agentic coding\")"
  max_results: 30

github_releases:
  repos:
    - "anthropics/claude-code"
    - "getcursor/cursor"
    - "Aider-AI/aider"
    - "openai/codex"
  min_body_length: 50
```

The config defines three source types:

- rss: Simon Willison's blog, and Hacker News for various AI agent-related content
- arxiv: research papers in categories relevant to LLM research or agent research
- GitHub releases: releases in repos related to popular coding agents

Yes, my own blog is also in here for reasons I'll share in a future blog post. And amusingly, I am noting as I write this that the Hacker News query is probably less comprehensive than I want it to be, and I have no idea why Aider is in here. I guess Claude thought I should care about it? It looks like maybe Aider is an outdated tool that was probably well-represented in Claude's training data, but with the last release in August 2025, it's pretty ancient in today's fast-paced AI world. Yet another example of why you should validate AI outputs; I should probably remove Aider and swap it out with some of the tooling that is currently keeping pace with AI development.

But, since this is a config, it's super easy to add, remove, or update sources, and the changes will automatically get picked up the next time I run the pipeline.

## Step 2: Fetch the inputs

The pipeline I mention here is a Python CLI. You run `news-gather run` and it does the whole thing: gather from all configured sources, fetch full content, tag everything, and save to the database. There's also `news-gather gather-only` for testing your sources without tagging, and `news-gather retag` for re-running the tagger on items that have content but didn't get tagged (usually because the API call failed the first time around). Both `run` and `retag` support `--dry-run` so you can see what would happen without writing anything to the database.

Each source type has its own gatherer class. The RSS gatherer uses `feedparser` to pull entries from the configured feeds. The arXiv gatherer uses the `arxiv` Python library to run the search queries. The GitHub releases gatherer hits the GitHub API directly with `requests`. All three apply a 48-hour lookback window, so I'm only ever picking up items published in the last two days. That overlap with the daily run schedule means I don't lose items if the pipeline fails one night or an item gets published late in the day.

## Step 3: Process the inputs

Each of these different content types gets separate handling to extract the actual text I want to feed into the system. ArXiv papers and GitHub releases already come with their content baked in: arXiv gives me the abstract, and GitHub releases give me the release body. Those get passed through as-is.

RSS is where it gets interesting. Some RSS feeds include the full article content in the feed entry itself (Simon Willison's blog does this). Others give you a stub with a link back to the page (Hacker News). The fetcher checks whether the RSS summary contains HTML and whether the extracted text is longer than 500 characters. If it is, it uses that directly. If not, it goes and fetches the full page.

For the full-page fetches, I'm using [trafilatura](https://trafilatura.readthedocs.io/), a Python library designed for web content extraction. It does a good job of stripping out navigation chrome, ads, and boilerplate to give you just the article text. There's a per-host rate limit of one second between requests so I don't get blocked for hammering someone's site. The trafilatura config uses a standard Mozilla user agent string, because some sites reject requests from non-browser user agents.

One thing worth noting: this design means adding a new source type to the config isn't *only* a config change. If I wanted to add, say, a Bluesky feed or a Substack newsletter source, I'd need to write a new gatherer class that implements the `Source` protocol (which is just a `gather()` method returning a list of `GatheredItem` objects). The config-driven design makes it easy to add new instances of existing source types, but new source *types* require code. That's a reasonable tradeoff for a pipeline this size. I'd rather have clean source-specific logic than a generic fetcher trying to handle every possible input format.

## Step 4: Tag the content

My pipeline uses an Anthropic API call to tag each piece of content. This is the part where the pipeline stops being a generic news aggregator and starts being something tailored to my specific workflow.

The gatherer feeds into multiple downstream pipelines that each need different kinds of source material (more on those in the result section below). Each pipeline gets its own tag, defined in a `tags.yaml` config with a description of what makes good and bad candidates for that pipeline. The tagger's system prompt includes full tag descriptions, so the LLM understands what each tag means in context, not just as a label.

The tagger sends each item's title, URL, summary, and up to 12,000 characters of full text to Claude Sonnet. The system prompt instructs the model to pick the single best-fit tag, only assign a second tag if the item genuinely serves both purposes well, and give zero tags to items that are off-topic or too thin to be useful. Most items get zero or one tags. Two is uncommon. Three should be very rare.

For each assigned tag, the model also writes a specific rationale explaining what the downstream pipeline would do with this item. Not generic reasoning like "impacts developer workflows," but concrete takes: what angle would that pipeline take on this content? This rationale helps the downstream pipelines later.

The response comes back as JSON with `summary`, `tags`, and `tag_rationale` fields. If the JSON parsing fails or the API call errors out, the item gets saved with zero tags. The `retag` command exists specifically for cleaning those up later.

Items without any `raw_content` (because the content fetch failed, or the page was a PDF, or a JavaScript-rendered SPA that trafilatura couldn't extract) skip tagging entirely. There's no point asking an LLM to categorize content it can't see.

## Step 5: Save it to a database

When this is all done, I save it to MongoDB Atlas, for the downstream consumers of the pipeline to use in separate jobs.

The schema for each item includes the URL (which doubles as the unique key), title, source metadata, the raw extracted content, the LLM-generated summary and tags, and a `consumed_by` object that tracks which downstream pipelines have already processed the item. That last field is the key design decision here. Each downstream pipeline can independently query for items that have its tag but haven't been consumed yet, process them, and mark them done. The pipelines don't need to coordinate with each other, and the gatherer doesn't need to know anything about what happens downstream.

The store handles the same URL showing up twice (maybe it appeared in both the Hacker News feed and Simon Willison's blog); the first one wins and the second is silently skipped. The unique index on `url` enforces this at the database level too. There's also a compound index on `tags` and `consumed_by` to make the downstream queries fast.

I chose MongoDB Atlas over something simpler (like a SQLite file or even a JSON dump) because I have multiple pipelines running as separate GitHub Actions jobs on a self-hosted runner, and I didn't want to deal with file locking or state management between jobs. Atlas is a shared data store that all the jobs can hit independently. It's overkill for the volume of data I'm dealing with (maybe 30-50 items per day), but the operational simplicity of "everyone just talks to Atlas" was worth it. And one of the downstream pipelines leans harder into Atlas functionality, which I'll talk more about in a future blog post.

## Result: Tidy, tagged news items, ready for the next stage in the pipeline

The `news-gather` pipeline is the first stage in a content production pipeline. It gathers, processes, and tags content so that three separate downstream jobs can each pull the items relevant to them:

- **shift-sourcing** consumes items tagged `shift`: opinion pieces, industry trend analysis, product launches with strategic implications, feature releases introducing substantive new capabilities. It processes these into draft material for the [aeshift.com](https://aeshift.com) site about how coding agents are changing software development practice.
- **research-sourcing** consumes items tagged `research`: academic papers proposing new methods, benchmark studies revealing limitations, unsolved problems in agent tooling. It surfaces open questions and gaps I might want to investigate.
- **upskill-sourcing** consumes items tagged `upskill`: tutorials, workflow guides, tool comparisons with concrete examples, posts showing real-world usage patterns. It organizes practical learning material for practitioners.

Each downstream pipeline queries Atlas for items with its tag that haven't been consumed yet, does its work, and marks them done. The pipelines don't coordinate with each other. They don't even know about each other. They just pull from the same pool of tagged items independently.

Each of these is its own repo, its own GitHub Actions workflow, and its own article in this series. I'll cover them in future posts.

The whole chain runs on a self-hosted GitHub Actions runner. The `news-gather` pipeline kicks off at 06:00 UTC every morning. The downstream jobs follow at staggered intervals: research-sourcing at 06:20, shift-sourcing at 06:40, upskill-sourcing at 07:00, and a dailies aggregator at 07:20. By the time I sit down with my morning coffee, I have a fresh set of tagged, processed, and organized content waiting for me. The pipeline can also be triggered manually from the GitHub Actions UI if I want to run it off-schedule.

This is the part of the project where the two problems I mentioned at the top converge. Building this pipeline gave me practical exposure to a real AI workflow (config-driven data ingestion, LLM-based classification, multi-stage pipeline orchestration), and the pipeline itself solves the discovery problem by filtering the AI firehose down to the content that's actually relevant to what I'm working on. I get to learn by building, and the thing I build helps me learn what to build next.
