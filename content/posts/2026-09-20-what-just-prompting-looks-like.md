---
title: What "Just Prompting" Looks Like
author: Dachary Carey
layout: post
description: In which I have Claude audit my own prompts to find out what "just prompting" means, and which parts of it anyone can copy.
date: 2026-09-20 11:00:00 -0500
url: /2026/09/20/what-just-prompting-looks-like/
image: /images/what-just-prompting-looks-like-hero.jpg
tags: [ai]
draft: true
---

A colleague asked me this week how I've built the things I've built over the past year: the [Agent-Friendly Documentation Spec](https://agentdocsspec.com), the [afdocs](https://afdocs.dev) checker, [agentsummons](https://github.com/agent-ecosystem/agentsummons) and [agentminutes](https://github.com/agent-ecosystem/agentminutes), [Agent Skill Implementation](https://agentskillimplementation.com), the [Agent Reading Test](https://agentreadingtest.com). He characterized it as "just prompting," and then asked whether there was something in how I prompt that we could teach non-technical colleagues in his department.

I was skeptical. My private theory was that whatever works for me works because I have enough domain background to notice when the agent is wrong, and that isn't something you can hand someone in a workshop. But a private theory is exactly the kind of thing I'd tell an agent to go verify, so I did the same thing to myself. I had Claude Code read every prompt I've given it since December 2025 and tell me what it found.

The short version: the mechanics really are "just prompting," in that there's no technique in there worth the name. What's happening underneath is code review, in verbal form chatting with my agent. Most of the review habits turn out to be copyable by anyone. The part that isn't copyable is knowing when to use them.

## What I looked at

Claude Code keeps a history file of every prompt you've typed, and full session transcripts for the last thirty days. Between the two, I had 7,753 prompts across 857 sessions, from December 10, 2025 through September 20, 2026. I excluded slash commands and a few hundred headless evaluation runs where a tool of mine was feeding content to the model, since those weren't me talking.

| Measure | Value |
|---|---|
| Prompts | 7,753 |
| Sessions | 857 |
| Median prompt length | 28 words |
| Median prompts per session | 5 |
| Prompts containing a question | 60% |
| Prompts opening with "Ok," "Yes," "Yeah," "Great," "Cool," or "Sure" | 33% |
| Prompts that hedge my own claim ("I think," "from memory," "I'm not sure") | 11% |
| Prompts that set a constraint ("don't," "not until I've read it") | 12% |
| Prompts that paste something in (an error, a paragraph, an email) | 6% |
| Explicit pushback ("are you sure," "I don't think that's right") | 2% |

The full transcripts only go back a month, so for anything that needed the agent's reply as well as my prompt, the sample is August and September. The pattern counts came from regular expressions over the prompt text, so treat them as lower bounds. This was an afternoon's audit of my own logs; it would need a bigger sample to count as a study.

## Is it prompting?

If you're picturing prompt templates, role assignments, or "you are a senior engineer with twenty years of experience," there's none of that. The median prompt is 28 words. A third of them open with "Ok." The longest ones, at 50 words for a typical session opener, are context dumps: here's the repo, here's the file, here's the URL, here's what I already did, here's what I want to think through. Then the session runs as a conversation, five or six turns at a time.

Here is a fairly typical run of prompts, verbatim, from a release session:

> Ok, I've committed and CI is green. Let's tag and push the release

> Do we need to document any of the changes in the documentation site?

> We should probably generate a fresh minimal session and re-capture the affected cli.md examples from it. I'm concerned we'll forget about it otherwise.

> Can we add some details to CLAUDE.md that when we add a new documentation page, you should _not_ commit and deploy until I've had a chance to read it?

That reads like a standup with a teammate, because that is what it is. I report what I did outside the chat, ask what's next, catch something we'll forget, and set a rule about what the agent is allowed to do without me. None of it is a prompt in the sense my colleague meant.

## Every pushback was correct

One interesting finding came from the 2% of prompts where I told the agent I didn't believe it. Only about 158 prompts across nine months do this, and I could only check the ones from the last month against the agent's reply. But in all eight cases I could check, I was right and the agent had assumed instead of verified.

| What I said | What the agent had actually done |
|---|---|
| "Oh, friend, can you check that link again? I get a soft 404 there" | Checked the status code and byte count. Never read the body. |
| "When we say `curl` was a human, what are you basing that on? I've watched agents `curl` in coding agent sessions and I don't think I've ever seen a human `curl` for something on their own" | Carried a classifier label ("dev tool, never an agent") into prose without checking a single session. |
| "58% of traffic to llms.txt is human browsers. I find that hard to believe." | Treated browser security headers as proof of a person. 26 of the 46 "humans" had a Chrome user agent whose version disagreed with its own client hints. |
| "Build has been going for a really long time, are you sure it can complete successfully and isn't just hanging indefinitely?" | Assumed a long-running process was working. It had used nine seconds of CPU in 28 minutes. |
| "You're saying the served HTML embeds content twice, but when I view source I'm not seeing the content embedded twice" | Inferred "twice" from a marker name. The actual count was once in the DOM and eleven times in serialized script payload. |
| "Are you sure about that? I'm listed as the package maintainer, and I just set up the trusted publisher connection on npm" | Read stale registry metadata. |
| "Is this really practical? What would realistically be required to 'record the scan's network origin'?" | Wrote a spec requirement without checking what an implementation would need. |
| "I'd like to address both in this release. Otherwise the 'fix' feels disingenuous." | Proposed fixing the reported symptom and leaving the underlying gap. |

Every one of these has the same shape. The agent had a proxy for the truth: a status code, a label, a marker name, elapsed time, a registry field. It reported the proxy as if it were the thing itself. My pushback forced it to go get the primary evidence, and the primary evidence disagreed with the proxy.

The agent's own reply on the soft 404 is worth quoting, because it names the problem better than I could: "I made exactly the mistake an agent would make. I checked `status=200 size=56KB` and reported 'resolves fine' without looking at the body." I've written before about [how confident-sounding output can be wrong](https://dacharycarey.com/2026/03/30/confident-sounding-gibberish/) and about [the verification gap in AI content pipelines](https://dacharycarey.com/2026/03/29/ai-content-pipelines-verification-gap/). Reading my own logs, the gap is the same one, and closing it is most of what I do all day.

## What anyone can copy

When I went looking for the habits that recur across every project, I found a dozen. None of them require knowing the answer. They require making the agent show its evidence, which is a different skill.

**Ask where a claim came from.** "What are you basing that on?" "Is this a known, documented fact, or where did this come from?" "Are you speculating?" "Where is the fetch buffer information coming from? I was unaware of this as a limit." This is the rarest habit in my logs, under one percent of prompts, and it had the best hit rate of anything. A rule for anyone: before you use a number or a fact the agent gave you, ask where it got it.

**Compare the output to one thing you know is true.** "I've watched agents `curl` and never a human." "When I view source I don't see it twice." "I'm listed as the maintainer, I just set that up." I'm checking the agent against my own lived experience, which has nothing to do with expertise in the agent's domain. Every professional has lived experience of what customers say, what campaigns did, what a real lead looks like. That works the same way.

**Say how sure you are about your own input.** "I'm quoting this from memory so I'm not sure about what I'm saying." "I *believe* the Build folks did these updates." "My memory could be faulty." Eleven percent of my prompts hedge like this. It's the mirror of the first habit: I'm telling the agent which of my claims to verify instead of building on.

**Paste the thing instead of describing it.** The error text, the paragraph you object to, the email you're replying to, the pitch you received. One prompt in sixteen carries a pasted block, and one in ten a file path. Describing from memory is how drift starts.

**Tell it what you did outside the chat.** "Committed and pushed, CI is green - tag it." "I've deployed the site - confirm the new section is available." "I initiated the first fetch at 9:49PM local time in Vermont." The agent can't see the world. You are its instrument readings, and it can't reason about state you haven't reported.

**Ask for alternatives with costs, then choose.** "What other approaches might be viable?" "Can you help me think through the pros and cons?" "How do other projects handle this? We can't be the only ones in this scenario." Then: "Let's go with 4." "Implement option 2 plus the #1 mapping." The convention-seeking question in particular ("how do others do this") needs no expertise to ask and reliably surfaces things I hadn't considered.

**Ask it to find contradictions in its own work.** From a spec editing session: "`single-fetch-completeness` is about not requiring multiple fetches. But in other places we talk about progressive disclosure, and `embedded-data-serialization` specifically requests *breaking up* things. So how do we square these?" Followed by asking for a full pass for tensions between sections. The agent found more.

**Ask who the reader is.** "Most of the users of this tool aren't developers - they're documentation folks who just want to know what they need to do. Can we reframe this as text?" "Does an average docs person know or understand HTML conversion pipelines?" This is the technical-writer habit in my prompts, and it may be the most portable one. Every field has an audience the agent hasn't been told about.

**Ask what got skipped.** "Anything else we should roll in while we're working on testing?" "Have we adequately exercised this through ample test coverage?" "Any hanging threads from this session before I log off?" "Did you have the info you needed to perform this validation?" Agents finish the task you named and stop. They rarely volunteer the adjacent thing.

**Write decisions down somewhere that isn't the chat.** About 150 prompts ask to capture something to a file, an issue, a project instructions file, or memory. "Let's file an issue on the repo about `curl` so we can capture this thought and not lose it in this session." "You're about to get auto-compacted - can you capture your current state so you don't lose any important details?" Nothing important should live only in the conversation, because the conversation ends.

**Say what must not happen.** "You should _not_ commit and deploy until I've had a chance to read it." "Leave yourself out of the commit message." "I'm not sure I _want_ you to load the example skills in case they contain some sort of attacks." "Can we commit scripts without leaking details about my personal computer and setup to the public internet?" Twelve percent of my prompts set a constraint like this. Agents are eager, and eager needs fences.

**Ask why about the agent's own choices.** "Why do you need an OAuth token? Don't we invoke Claude headlessly?" "You did not ask me about the design choice here. Why *not* handle this correctly?" "What is that session link, and why is it there? What would somebody who isn't me see if they go to it?" That last one turned up a link to my private session logs in a public pull request description. I would not have noticed if I hadn't asked.

## What you can't copy

Look at the triggers in the pushback table again. I knew a 200 response can carry a 404 page because I've spent months [testing how agents fetch documentation](https://dacharycarey.com/2026/04/06/designing-agent-reading-test/). I knew agents shell out to `curl` because I've watched them do it in session logs. I knew the npm metadata was stale because I had configured the publisher myself ten minutes earlier. I knew 156KB of CSS could push content out of a truncation window because I'd seen it happen to my previous employer's docs.

None of those triggers can be taught in a workshop. The habits above are the questions; experience is what tells you when to ask them. So my skepticism was half right. A non-technical colleague can't prompt their way to my particular judgment about agent behavior, because the judgment came from a year of looking.

But the other half matters too. The habits work as a ritual even when you don't know the answer, because they force the agent from proxy to primary evidence, and the primary evidence is usually where the error is. And the colleagues my coworker was asking about have their own domain. Their department's equivalent of "I've never seen a human `curl`" exists, and they already know what it is. What they may not have is the reflex to say it to the agent instead of to themselves.

## Do my prompts read like an engineer's?

I also asked Claude to answer a question I couldn't answer about myself: whether my prompts read like an engineer or a non-technical person. I've been a professional writer since 2007 and only started calling myself a developer in the last few years, so I was curious what the text says.

Its answer was engineer, without much hedging, and the evidence it cited was less the vocabulary than the reasoning. Arguing that a diffing tool shouldn't exit with 1 for a diff because 1 conventionally means error. Asking whether a CI secret could be exfiltrated before adding it. Objecting that changing a type in a validator would obscure what the spec requires. Non-technical prompts describe symptoms; mine name mechanisms and propose where the fault is before asking.

Two things kept it from reading as pure engineer, in Claude's assessment. I pay constant attention to who will read the output and how a label lands. And I ask how other projects handle a problem before inventing an answer. Its phrasing was "a tech lead with a writer's ear for audience," which I'll take.

Somewhere in that verdict there's a lesson for the "just prompting" framing. The agent did the typing. The review and judgment was mine. If you want to know why one person's agent output is better than another's, look at what happened when the agent was wrong.
