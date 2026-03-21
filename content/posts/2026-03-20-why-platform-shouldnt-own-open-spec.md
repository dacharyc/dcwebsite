---
title: Why a Platform Shouldn't Own an Open Spec
author: Dachary Carey
layout: post
description: In which a platform breaks the Agent Skills spec for its own benefit.
date: 2026-03-20 07:00:00 -0500
url: /2026/03/20/why-platform-shouldnt-own-open-spec/
image: /images/why-platform-shouldnt-own-open-spec-hero.png
tags: [ai, coding]
draft: false
---

The Agent Skills spec claims to be "A simple, open format for giving agents new capabilities and expertise." But the creator of Agent Skills, Anthropic, is also the company behind the spec's maintenance. And when a platform both owns a spec and competes with the platforms implementing it, the incentives stop being aligned. Let me tell you a tale of how Anthropic has quietly broken Agent Skill interoperability with every other platform, encoded it as ground truth in a spec that claims to be open, and what that means for anyone publishing or consuming skills with portability in mind.

## What is a spec, and why does ownership matter?

Before we get into the specifics, let's talk about what a specification actually does. A spec is an implementation guide. It tells everyone building on a format exactly what to expect: which fields exist, what values are valid, how files should be structured, what behavior is required vs. optional. When you write an Agent Skill, you're writing to the spec. When a platform like GitHub Copilot, Cursor, or Roo Code loads your skill, it implements the spec. The spec is the contract between creators and consumers.

This contract only works if everyone can trust it. In well-governed specs (think HTTP, HTML, or OpenAPI), a standards body or community process manages changes. There are version numbers. There are changelogs. There are review periods before breaking changes land. Multiple stakeholders have a voice. No single vendor gets to unilaterally rewrite the rules to suit its own product.

Agent Skills doesn't work this way.

## History of the Agent Skills spec

Agent Skills started life as a Claude Code feature. Anthropic built the format, documented it in their own platform docs, and shipped it as part of their coding agent. The concept was straightforward: a `SKILL.md` file with YAML frontmatter (name, description, allowed tools) and a markdown body containing instructions for the agent. Skills could optionally include `scripts/`, `references/`, and `assets/` directories. That was the whole format.

In December 2025, Anthropic "gave" the spec to the community. They created the [agentskills](https://github.com/agentskills/agentskills) GitHub organization, published the spec and a reference SDK, and launched [agentskills.io](https://agentskills.io) as the canonical documentation site. Other platforms adopted the format. Roo Code, Goose, Gemini CLI, Cursor, OpenAI Codex, and over 20 others added their logos to the adoption carousel. It looked like an open standard taking shape.

But the governance didn't change with the branding. The [agentskills](https://github.com/agentskills/agentskills) GitHub organization has two members. One is a product manager at Anthropic. The other, jonathanhefner, has authored 33 of the repository's commits and merged every significant PR since the repo's creation. His GitHub bio describes him as a "software craftsman." As of March 19, when I started digging into the details around the project, the maintainer's LinkedIn profile showed he is a contractor working for Anthropic since October 2025 as a "Technical Documentation and Content Engineer."

This is not unusual for early-stage open source projects. Someone has to do the work. The problem isn't that Anthropic employees maintain the spec. The problem is what happens next.

## Changes to an unversioned spec

Since the spec launched in December 2025, a steady stream of changes have landed. Some are genuinely helpful: better docs, clearer examples, new guides for skill creators. But mixed in with the documentation improvements are changes that alter what a valid skill looks like and how implementations should behave. And the spec has no version number.

Think about what that means in practice. A spec is an implementation guide. Platform teams at Cursor, Roo Code, and GitHub Copilot read the spec at some point and built their skill-loading logic to match. If you're a technical writer publishing an official Agent Skill for your company's product, you read the spec and wrote your skill to match it. But "the spec" is a moving target. There's no v1.0 or v1.1. There's no changelog entry that says "in this release, we changed X." There's just a website that looks the same every time you visit it, with content that has silently shifted underneath.

Here are some of the changes I've tracked in just the last month.

### The `allowed-tools` type ambiguity

The spec defines `allowed-tools` as "a space-delimited list of tools." That description is ambiguous about the YAML type. "Space-delimited" suggests a scalar string (`allowed-tools: Read Write Bash`), but "list" suggests a YAML sequence. I [analyzed hundreds of community-published skills](https://github.com/agentskills/agentskills/issues/144) and found three formats in active use: space-delimited strings (matching the spec's literal wording), inline YAML sequences like `[Read, Write, Bash]` (the most common format, used by 17 of 36 skills with this field), and block YAML sequences. The spec doesn't say which is correct, and different platforms handle the ambiguity differently. A skill using the "wrong" format for a given platform may silently lose its tool permissions.

What I didn't even realize at the time is that the `allowed-tools` *concept* is broken. Those tool names are Claude-specific. Claude's `Read` tool is GitHub Copilot's `read_file`. So the concept of defining tools that are allowed when using a skill is, itself, broken if you're thinking about portability. More on that in a minute.

### The directory structure goes from closed to open

In March 2026, [PR #216](https://github.com/agentskills/agentskills/pull/216) added a `└── ...  # Any additional files or directories` entry to the spec's directory tree. The PR was titled "Visually clean up the specification page" and described as having "**no semantic changes**." But this is a meaningful change for implementers. Previously, the spec's directory tree showed only `SKILL.md` plus three optional directories (`scripts/`, `references/`, `assets/`). The `...` entry signals that any files and directories are permitted.

The spec maintainer [argued](https://github.com/agentskills/agentskills/issues/262#issuecomment-4099098602) that the directory structure was always an open set, pointing to the phrases "at minimum" and "such as" in the spec text. That's linguistically defensible. But the directory tree diagram showed only four entries with no indication of additional ones, and an earlier version of Anthropic's own skill guidance told authors to avoid adding extraneous files, noting that "the context window is a public good." When the platform's own guidance tells authors to minimize what's in a skill directory, a conservative implementer reading a tree that lists only four entries is going to treat that as the set. Whether Anthropic intended it as a closed set or not, implementers built real validation and loading logic treating it as one. That makes the ambiguity load-bearing: it didn't matter until something changed, and then it broke things.

The maintainer closed [the issue](https://github.com/agentskills/agentskills/issues/262) and opened [PR #268](https://github.com/agentskills/agentskills/pull/268) to clarify the language. The fact that clarification was needed validates that the spec was ambiguous enough to mislead implementers. This is exactly the kind of change that would benefit from the visibility mechanisms (versioning, changelogs, labels) that the spec currently lacks.

Why does this matter beyond the specific case? Because implementations that validated against the closed set were correct before this change and are now arguably non-compliant. Validators that warned about unrecognized directories were doing the right thing. Now the spec says those directories are fine, but gives no guidance on how platforms should handle them. Should a platform ignore unknown directories? Load their contents into agent context? Warn the skill author? The spec doesn't say.

The change was motivated by Anthropic's own [evaluating-skills guide](https://agentskills.io/skill-creation/evaluating-skills), which recommends storing `evals/evals.json` inside the skill directory. Rather than adding `evals/` as a recognized directory with documented behavior, the maintainer [confirmed](https://github.com/agentskills/agentskills/issues/238#issuecomment-2722640390) that `evals/` "falls under the `...`" and that the evals guide is "non-normative." In other words: Anthropic's own tooling needs this directory, so the spec was widened to accommodate it, but without any of the guidance that would let other platforms handle it correctly.

### Implementation guidance rewrites

[PR #200](https://github.com/agentskills/agentskills/pull/200) replaced the entire `integrate-skills.mdx` page with a 335-line guide introducing new architectural concepts for implementers: progressive disclosure, lenient YAML parsing, catalog XML format, behavioral instruction templates, permission allowlisting. This isn't a change to the specification file itself, but it's a complete rewrite of the page that tells platforms how to implement the spec. An implementer who read the old integration guide and built against it would have no signal that the guidance was entirely replaced.

### The `compatibility` field

The spec includes a `compatibility` field for declaring runtime requirements, with examples showing platform targeting (`claude-code >= 1.0`) and runtime version pinning. The field is a free-text string with no structured format, which means every platform has to decide independently how to parse requirements like `Requires Python 3.14+ and uv`. There's no guidance on whether platforms should validate these requirements, warn users, or block activation. The field enables platform-specific targeting in a supposedly platform-neutral spec, and the example `Designed for Claude Code (or similar products)` appears in the spec itself.

### Skill-to-skill invocation

Community members have [reported](https://github.com/agentskills/agentskills/issues/95) that Claude Code's system prompt imposes undocumented restrictions on skill-to-skill invocation (preventing infinite loops by limiting same-skill calls), while other platforms like GitHub Copilot don't enforce these restrictions. The spec says nothing about whether skills can invoke other skills. Every platform made its own decision, and those decisions are invisible to skill authors.

## Incentive misalignment in an "open" spec

These aren't random oversights. They follow a pattern. Anthropic builds a feature in Claude Code, then adjusts the spec to accommodate it. The spec change lands as a documentation improvement or a new guide, not as a breaking change. Other platforms don't get notified. There's no version bump to trigger a review.

Consider the `...` directory change from the perspective of each stakeholder:

**Anthropic** wants `evals/` in the skill directory because their eval tooling expects it there. Adding `...` to the spec makes their tooling compliant without having to formally specify a new directory type. Quick, clean, done.

**Other platforms** (Cursor, Roo Code, Copilot, etc.) implemented the spec when it defined a closed directory set. They may be ignoring `evals/`, loading it into context by accident, or warning about it. They have no way of knowing the spec changed, because there's no version number to check and no notification mechanism.

**Skill authors** who relied on the closed directory model to keep their skills portable now have no signal about which directories are safe to use across platforms. The spec says "anything goes," but every platform handles "anything" differently.

**Validators** (like [skill-validator](https://github.com/agent-ecosystem/skill-validator)) that check skill structure against the spec are caught in the middle. Warn about unrecognized directories and you're flagging something the spec now permits. Stay silent and you're letting authors ship skills that may break on platforms that haven't caught up to the latest spec revision.

This is the core of the incentive problem. Anthropic benefits from a spec that tracks their product. Everyone else benefits from a spec that's stable, versioned, and platform-neutral. These goals conflict, and right now, Anthropic's goals win by default because they control the repo.

The community contribution pattern reinforces this. There are 30+ open issues from community members proposing features like versioning, JSON Schema validation, credential handling, and skill dependencies. Many have been open for months. Meanwhile, the spec maintainer has merged 16 of his own PRs since February, adding guides, examples, and structural changes. Community PRs that don't add logos to the carousel tend to sit.

## How a broken spec leads to subtle bugs

If you're a technical writer or developer creating Agent Skills for your company's product, here's what this means for you in concrete terms. A spec is supposed to be a reliable implementation guide; when it's quietly shifting, the things that break aren't obvious crashes. They're subtle, silent failures that you might not notice until a customer reports them.

**Frontmatter that works on one platform but not another.** You write `allowed-tools` as an inline YAML sequence because that's what most community skills use. It works perfectly in Claude Code. But another platform's parser expects a space-delimited string, because that's what the spec literally says. Your skill loads, but the agent doesn't have permission to use any of the tools it needs. It fails silently, or produces worse output, and neither you nor the user knows why.

**Directories that are invisible on some platforms.** You add an `evals/` directory following Anthropic's evaluating-skills guide. Claude Code knows what to do with it. Other platforms see an unrecognized directory. Some ignore it entirely. Some load `evals.json` into agent context, burning tokens on test fixtures during real user sessions. Your skill works on the platform you tested against, and subtly degrades on others.

**Tool references that don't exist.** Claude Code's tool names (`Read`, `Write`, `Bash`, `Glob`, `Grep`) are specific to Claude Code. The spec lists them in its examples without noting that they're platform-specific. A skill that declares `allowed-tools: Read Write Bash` is encoding Claude Code's tool vocabulary. Other platforms have different tool names, different capabilities, and different permission models. The skill may appear to load correctly but produce errors or degraded behavior when the agent tries to use tools that don't exist in that platform's runtime.

**Behavioral differences that the spec doesn't address.** How deep can directory nesting go? Can a skill invoke another skill? What happens when a skill references a file outside its directory? How should platforms handle resource loading order? The spec is silent on all of these, so every platform made its own choices. Your skill works because it happens to match the assumptions of the platform you tested on.

## Why this matters

If you're distributing official Agent Skills on behalf of your company, the pitch for Agent Skills is portability. One skill format, many platforms. Your customers use Claude Code, Cursor, Copilot, Roo Code, Gemini CLI, and a growing list of others. You write one skill and it works everywhere.

That's the promise. The reality is that the spec is quietly biased toward one platform, and the bias is growing with each undocumented change. Unless you are actively monitoring the spec repo's commit history, reading every merged PR, and testing your skills across multiple platforms, you will miss the drift. And the drift is cumulative. Each small change is easy to overlook in isolation. Taken together, they add up to a spec that increasingly describes Claude Code's behavior rather than a platform-neutral standard.

For developers and end users, the situation is even more opaque. When you install a skill published by a company or a community member, you have no way of knowing which version of the spec it was written against. You don't know whether the author tested it on multiple platforms. You don't know whether it relies on platform-specific tool names, directory structures, or behavioral quirks. If it doesn't work well in your agent, you'll probably blame the skill or the agent, not the spec. But the spec is often the root cause.

## What we can do about it

The technical fixes are obvious. Version the spec. Publish changelogs. Establish a review process for changes that affect interoperability. Give community stakeholders a real voice in governance, not just a logo on a carousel. These are solved problems in the standards world.

But Anthropic has no incentive to do any of this. The current arrangement is perfect for them: they get the marketing benefit of an "open" spec while retaining unilateral control over what that spec says. Individual community members filing issues won't change that calculus.

I've tested this directly. I [filed an issue](https://github.com/agentskills/agentskills/issues/262) documenting the interoperability implications of the directory structure change. The maintainer closed it, arguing that the spec was always an open set and that the `...` addition in PR #216 was not a semantic change. He then opened [PR #268](https://github.com/agentskills/agentskills/pull/268) to clarify the spec language. Consider what that sequence means: the issue was closed on the grounds that the spec was already clear, and then a PR was opened to make the spec clearer. If the language didn't need clarifying, the PR wouldn't exist. If it did need clarifying, then the original change was meaningful enough to warrant visibility, which is the point the closed issue was making.

I [filed a proposal](https://github.com/agentskills/agentskills/issues/265) asking for the minimum viable governance change: a spec version identifier, a changelog for normative changes, and a label on spec-altering PRs. I filed it as an issue deliberately, because I'd already observed that community discussions go unactioned. The maintainer converted it to a [discussion](https://github.com/agentskills/agentskills/discussions/269), disputed the supporting examples, and invoked the repository's AI disclosure policy. The conversion itself demonstrated the pattern I was trying to address: community input gets routed to discussions, discussions don't get actioned, and the maintainer's own spec-altering PRs bypass that process entirely.

After a back-and-forth in [discussion #269](https://github.com/agentskills/agentskills/discussions/269), the maintainer ultimately said he has "for the most part, no objections" to the three proposals and agrees that "spec changes (if and when they happen) should be visible." That qualifier is doing a lot of work. Throughout the thread, he disputed every supporting example, maintained that no spec-altering changes have occurred, and characterized the integration guide rewrite as irrelevant because "guides aren't the spec." So the position is: yes, spec changes should be visible, but nothing has changed, so there's nothing to make visible. Whether that leads to actual versioning, a changelog, or PR labels remains to be seen.

### The partner pressure path

The pattern that has actually worked, historically, is institutional pressure from organizations that control something the company needs. When the Apache Software Foundation banned Facebook's BSD+Patents license from all Apache projects in 2017, and WordPress announced it would stop using React, Facebook relicensed React under MIT within weeks. It wasn't thousands of GitHub comments that moved Facebook. It was two institutions that controlled distribution channels saying "we won't ship your code."

The analogue here is partner relationships. Anthropic has business partnerships with the companies publishing Agent Skills. If those companies, through their partner channels, start pointing out that the spec's governance is creating real portability problems for their customers, that's a conversation Anthropic can't easily ignore. One technical writer blogging about it? Easy to dismiss. Five enterprise partners raising it in quarterly business reviews? That's a product decision.

This is the most realistic path to change. If you work at a company that publishes Agent Skills and has a partnership with Anthropic, raise this with your partner team. Bring specifics: the unversioned spec, the breaking changes landed as "visual cleanups," the `allowed-tools` ambiguity, the platform-specific tool names baked into a supposedly portable format. Partner teams speak a different language than GitHub issues, and they have leverage that community contributors don't.

### The fork path

The other option is to fork the spec, version it, and try to get platforms to adopt the fork. This has precedent, and recent precedent at that.

In 2024, Redis switched to a proprietary license. Within weeks, the Linux Foundation announced Valkey, backed by AWS, Google, Oracle, and Snap. Within six months, Valkey shipped version 8.0 with features Redis didn't have, Fedora dropped Redis entirely, and Redis saw a 20% decline in new contributions. By May 2025, Redis's CEO publicly admitted the license change was a mistake. The same year, OpenTofu forked Terraform after HashiCorp adopted the Business Source License. OpenTofu crossed 10 million downloads and started shipping features Terraform didn't have, like state encryption.

These forks succeeded because the companies with the largest deployments and the deepest engineering benches aligned on the same fork simultaneously. Cloud providers and ecosystem tooling vendors drove both efforts, not individual contributors.

The Agent Skills situation is different in a critical way. Valkey and OpenTofu forked because of license changes that threatened existing business models. Agent Skills hasn't done that. The spec is Apache 2.0, and the governance problem is subtle: it's about who controls the direction, not about access to the code. That makes it harder to rally fork energy, because nobody's business is under immediate threat. The pain is diffuse. It shows up as "my skill works on Claude Code but not Cursor" rather than "I can no longer legally distribute this software."

A fork could still work, but it would need a neutral home (Linux Foundation, OpenJS Foundation) and buy-in from at least two or three of the platforms that aren't Anthropic. Concretely, that means platform engineers who maintain skill-loading code agreeing to validate against the forked spec, and their organizations committing reviewer time to a governance process. Without that, a fork is just one more repo. This path requires the same institutional weight as the partner pressure path; it's just routed through a different channel.

### What's realistic

Realistically, the most likely near-term outcome is that nothing changes. Anthropic will keep evolving the spec to match Claude Code. Other platforms will keep drifting. Skills will keep breaking in subtle ways that authors don't notice and users can't diagnose.

The most likely path to change is not community pressure but commercial pressure. When enough enterprise partners tell Anthropic that the governance gap is creating real support costs and customer confusion, the math shifts. Until then, if you're publishing skills and you care about portability, you need to test on multiple platforms yourself, because the spec won't protect you.
