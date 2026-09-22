---
title: Does Your Agent Know What It Can Do?
author: Dachary Carey
layout: post
description: In which I dig into how Claude Code teaches the model about its own harness, because most agents have no idea what they can actually do.
date: 2026-07-21 07:00:00 -0500
url: /2026/07/21/agent-know-what-it-can-do/
image: /images/agent-know-what-it-can-do-hero.jpg
tags: [ai]
draft: true
---

I've been building some new tools lately, and have noticed some interesting patterns in Claude Code around discovering its own capabilities and limitations. It reminded me of some conversations I've been having with fellow AI researcher, [Rhyannon Rodriguez](https://www.rhyannon-rodriguez.com), about how agent harnesses often lack introspection/awareness and debugging around their own capabilities. I think there are interesting observations here for agent harness developers, and open questions for the rest of us, about how agents understand our products.

## Agents may lack information about their capabilities

Rhyannon and I have had a lot of conversations about how the various coding agent harnesses - Claude Code, Codex, Cursor, Copilot, and others - lack awareness of their own configuration and capabilities. In their deep cross-harness testing, Rhyannon ran into issues more than once where an agent needed a specific configuration lever pulled or knob twiddled *but the agent didn't know it*. It either failed, worked around, or hallucinated its way through the problem without really understanding what was needed or how to fix it. We have come to realize that most models don't have any particular or deep knowledge of how their harness works, and most harnesses don't bother to encode this into any harness-provided information for the agent.

My favorite example from Rhyannon's testing involves Windsurf's Cascade harness. One of their test cases uncovered a bug in Cascade's `read_url_content` tool; it rewrote the requested URL before fetching, transforming a Claude API docs URL into an `llms-full.txt` path that redirected to a 404. No agent ever received the target content, because no agent ever actually requested it; the harness swapped the resource out from under them before the network call was made.

Five agents ran the same prompt against the same URL, and their responses were all over the map. One reported the error verbatim and stopped. One acknowledged the failure and gave up. One surfaced an undocumented internal constant (`CORTEX_STEP_TYPE_READ_URL_CONTENT`) that leaked through unsanitized from the tool layer. One retried against a different domain and got roughly 40 KB of 404 error page HTML for its trouble. Exactly one agent out of five correctly diagnosed the root cause as internal URL rewriting: Cognition's own SWE-1.6 model, running in Cognition's own harness. As Rhyannon notes in [the Cascade friction notes](https://rhyannonjoy.github.io/agent-ecosystem-testing/docs/cognition-windsurf-cascade/friction-note-interpreted), that pattern suggests trained familiarity. The only model that understood what the harness was doing to its request was the one built by the people who built the harness.

## Agents don't tell you when you're doing it wrong

Because models lack details about harness capabilities, and maybe also because of the current generation of reinforcement learning techniques, Rhyannon and I have found that most models don't correct users when they're "doing it wrong." 

For example, as part of Rhyannon's testing track, they ask the agent to use whatever the harness equivalent is of its web fetch tool to retrieve webpage content during testing. Coding agent platform docs have been inconsistent about whether they document this tool, so Rhyannon has used various sources to find the name of the tool for invocation. Rhyannon found that in some cases, the tool name was outdated or incorrect, but *agents didn't correct it.* Agents elided the incorrect tool names in user prompts instead of telling the user they were providing incorrect inputs.

The clearest published example is [Cursor's `@Web`](https://rhyannonjoy.github.io/agent-ecosystem-testing/docs/anysphere-cursor/friction-note). Rhyannon's testing framework invoked `@Web` believing it triggered a web fetch, when it's actually context-attachment syntax (and syntax Cursor 2.0 removed from the context menu, at that). Cursor never once flagged the misuse. It actively reinforced the misconception by reporting `@Web` in its tool usage logs, run after run, despite having access to Cursor's own documentation. Each successful-looking test run built false confidence in a methodology that was wrong about the mechanism it was testing.

In practice, this manifests in a few different approaches:

- The agent checks for the requested tool, doesn't see it, and halts the task
- The agent checks for the requested tool, doesn't see it, and uses some other method to complete the task
- The agent fails to understand the request *because* it doesn't see the requested tool, and takes some other action based on a different interpretation of the task

In cases where the agent uses some other method to complete the task, it often doesn't surface that change in approach to the user. This manifests as the user seeing it do something other than what was requested. 

In other cases, where a given request fails because of a configuration issue, we've observed agents are very inconsistent about whether they have the capability to debug the issue and help the user fix it. In most cases, agents don't seem to understand *why* they're unable to complete a task or that a configuration or setting needs to change. They may spend many turns/tokens trying different workarounds, and may eventually find some way to complete the task, or may fail without ever actually attempting to understand the configuration issue that prevented them from reaching success. Some harnesses/models do a better job and can diagnose there is a configuration issue in some cases, but this seems to be an exception and not the rule.

## How I've observed Anthropic solve this problem

I do think Anthropic is leading the field in these issues with Claude Code. Over the last few months, I've observed consistent improvements in how Claude Code discovers and checks product capabilities and limitations before implementing.

### Information bundled with the harness

First, a note about how Claude Code ships, because it matters for everything in this section. Unlike harnesses that install as a directory full of readable files, Claude Code on my machine is a single ~237MB binary. Anthropic compiles reference documentation directly *into* that binary, which means the docs version with the harness, every release, automatically. And it means everything I'm about to describe is verifiable: run `strings` over the binary yourself and you can read all of it. (Pleasingly recursive disclosure: Claude Code helped me extract these strings from its own binary.)

#### Release notes/changelog

Claude Code fetches its own changelog from [the GitHub repo](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) and caches it locally at `~/.claude/cache/changelog.md`, refreshing it periodically. Two things about this mechanism surprised me:

- The cache tracks the repo's main branch, not your installed version. When I checked, my cached changelog described version 2.1.212 while my installed binary was 2.1.205. The harness is sitting on release notes for features *newer than the running build*.
- Because it's a plain markdown file under `~/.claude/`, the model can read it in-session. And the entries are unusually detailed for a changelog: exact environment variable names, default values, flag syntax. These aren't marketing bullet points; they read like they're written for both humans and models to act on.

There's a human-facing layer here too. The `/release-notes` command surfaces the changelog in-session, and there's a `/powerup` command whose description in the binary reads "Discover Claude Code features through quick interactive lessons." That one is aimed at people rather than the model, but it's part of the same story: the harness actively teaches its own capabilities instead of assuming you'll go read a docs site.

#### Skill-style documentation

The more substantial mechanism is a full `claude-api` skill compiled into the binary. This is not a single markdown file. It's a proper multi-file skill: per-language reference files (Python, TypeScript, Java, Go, Ruby, C#, PHP, and raw curl), plus shared files like `shared/error-codes.md`, `shared/prompt-caching.md`, `shared/model-migration.md`, and per-language streaming and tool-use references.

What struck me reading the extracted content is that it's opinionated *working* knowledge, not API listings. It contains compilable code skeletons, known compiler errors with their fixes, and explicit anti-patterns. My favorite example, from the C# reference: "Do not escalate to a `dotnet run` reflection probe... producing a `Program.cs` and iterating beats researching." That's not documentation in the traditional sense. That's process guidance for how an agent should spend its turns.

Because the skill ships inside the binary, it gets re-versioned with every release. This is how Anthropic keeps the model's knowledge of its own API current past the model's training cutoff, without requiring a network call: the harness updates far more often than the model does, so the harness carries the fresh knowledge.

### Claude Code documentation map

Bundled documentation solves the "stale knowledge" problem, but only up to the last release. For current information, Claude Code maintains a [documentation map](https://code.claude.com/docs/en/claude_code_docs_map.md): an auto-generated index of the entire Claude Code docs site, rebuilt daily by GitHub Actions, designed (per its own header) "for easy navigation by LLMs."

I wondered whether this was just a rebranded [llms.txt](https://code.claude.com/docs/llms.txt), since Claude Code publishes one of those too. It isn't; the two files take different approaches to the same problem:

- **The docs map** is hierarchical: roughly 25 category groups covering 140-150 pages, where each page entry includes the page's *full heading outline* but no descriptions. It's optimized for answering "which page contains the section I need?"
- **The llms.txt** is the standard llms.txt shape: a flat list of about 200 links, each with a one-to-three sentence description but no heading outlines.

Here's something worth noting: Anthropic points its own agent at the docs map, not the llms.txt. When they needed an index for their own agent to navigate their own docs, they built a custom format that trades prose descriptions for structural outlines. That's a signal about what they think agents actually need from a docs index, and it's worth paying attention to if you're publishing an llms.txt and assuming it's sufficient. I've recommended llms.txt as a wayfinding artifact partially because so many build systems are already shipping it and I've observed it help agents in practice. Anthropic has presumably been doing their own research and decided this approach is better for agents.

### Explicit instructions for Claude Code

So Claude Code bundles reference material and publishes live indexes. But none of that matters if the model doesn't *use* them; models are very confident about what they remember from training, and what they remember about a fast-moving product is reliably stale. This is where Claude Code gets aggressive, in three layers.

**Layer one: a trigger injected into every session.** The `claude-api` skill's description, which sits in the model's context in every conversation, is written as a hard directive. Quoting from what I extracted:

```text
TRIGGER — read BEFORE opening the target file; don't skip because it "looks like a one-liner" — whenever: the prompt names Claude/Anthropic in any form... the user asks about an LLM (pricing/model choice/limits/caching) — never answer from memory...
```

"Never answer from memory." The trigger fires on any mention of Claude or Anthropic, any LLM question, or any LLM-shaped task where the provider isn't stated. It even anticipates the specific failure mode where the model decides a task is too trivial to bother checking ("don't skip because it looks like a one-liner"). And it includes a skip rule with a literal grep command the agent should run to check whether the project uses a *different* provider before loading Anthropic-specific guidance.

**Layer two: live-source pointers inside the bundled docs.** The embedded reference files repeatedly end with pointers like "WebFetch via `shared/live-sources.md`": a bundled file of URLs to fetch when the static reference isn't enough (SDK example repos, current beta headers, that sort of thing). The pattern is bundled-docs-first, network-as-escalation. The agent gets fast answers for the common case and a sanctioned path to current information for the edge cases.

**Layer three: a dedicated docs-fetching subagent.** Claude Code ships a built-in agent called `claude-code-guide`, and the main agent is instructed to delegate to it for any "Can Claude...?", "Does Claude...?", or "How do I...?" question about Claude Code, the Agent SDK, or the API. I extracted its system prompt from the binary. It hardcodes the live doc indexes (the docs map for Claude Code and Agent SDK questions, `platform.claude.com/llms.txt` for API questions, and a separate index for Claude in Slack), and gives the agent a four-step procedure: figure out which domain the question falls into, fetch the appropriate docs map, identify the relevant pages, fetch those pages. It runs on Haiku, because a docs lookup doesn't need the expensive model.

My favorite detail is the failure instruction. When the guide agent can't find an answer, or the feature doesn't exist, it's told to direct the user to file an issue on the GitHub repo. Not to guess. Not to hallucinate a plausible-sounding config option. To say "this doesn't appear to exist, here's where to report that." That is exactly the behavior Rhyannon and I found missing across most of the harnesses we've poked at.

There's also ambient currency baked into the system prompt itself: current model IDs and names, the model's knowledge cutoff date, today's date, and an instruction to default to the latest models when building AI applications. Small stuff, but it's the harness explicitly correcting the model's stale knowledge about its own product line.

## What can the rest of us do?

If you're building an agent harness, the takeaways feel pretty direct. Your model doesn't know what your harness can do; that knowledge has to come from somewhere, and "somewhere" is you. Bundle capability documentation with the harness so it versions together. Give the agent an explicit, sanctioned path to current information, and instructions aggressive enough to overcome the model's confidence in its stale training data. And teach the agent how to fail: "this feature doesn't exist, here's where to report that" provides a better user experience than working around a request the harness can't fulfill.

For the rest of us, the observation carries different implications. Everything Rhyannon and I observed about agents not knowing their *harness* applies equally to agents not knowing your *product*. The model's knowledge of your API is frozen at its training cutoff, and it will confidently implement against that frozen version unless something intervenes. Anthropic's intervention pattern is replicable: bundled skills that ship opinionated working knowledge, live indexes designed for agent navigation, and explicit check-before-implementing triggers. Some of this maps onto work I've already been doing around [agent-friendly documentation](https://agentdocsspec.com); some of it, like the docs map's structural-outline format outcompeting llms.txt inside Anthropic's own harness, raises questions I don't have answers to yet.

What I do know is that "the agent will figure it out" is not a strategy. The agents that figure it out are the ones whose builders decided that self-knowledge was a product surface, and built it.
