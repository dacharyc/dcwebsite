---
title: Can Agent Skills Make Output Worse?
author: Dachary Carey
layout: post
description: In which I discover that a skill made LLM output 66% worse, and dig into why authoring official skills is a full product lifecycle, not a deliverable.
date: 2026-05-07 22:00:00 -0500
url: /2026/05/07/can-agent-skills-make-output-worse/
image: /images/can-agent-skills-make-output-worse-hero.jpg
tags: [ai]
---

In the last ~6 months, Agent Skills have gone from a new feature introduced by Claude Code to a widely adopted paradigm across the industry. Companies that want to signal they understand customer AI adoption and consider it as part of the developer experience have been publishing official skills. I have been part of such an effort at my own company, where I have learned a ton from our experience and from analyzing other releases across the industry. I went in expecting a documentation-pipeline problem. I came out understanding that official skills are a full product surface; one that can actively make your customers' agent output worse if you ship them naively.

## Should you generate skills from documentation?

When our skills project started, my role sat in the docs org; I proposed we should offer official company skills derived from our documentation. As someone who has spent a decade in documentation, I know content maintenance is a huge burden. I wanted to create skills using a pipeline where we could regenerate them whenever we updated the corresponding documentation. This would give us an automated way to keep up-to-date with changes to the documentation, using product updates and the pipeline from product to documentation as the canonical pipeline to also update our skills.

I built a system. I was able to successfully generate skills from a manifest structure I defined. The skills contained useful information from our documentation. If you're curious what that looked like, it's in a spike GitHub repo here: https://github.com/dacharyc/mdb-skill-builder

So once I could successfully generate a skill, my next task was to figure out if the skill I created from our documentation was actually any good.

## Do skills actually help?

Skills are new, and we're still trying to figure out what should be in them and how to make them effective. My initial approach, when I started thinking about what it would take to do this, was to run some evals on the skills I created from our docs. So I bring you "baby's first eval": https://github.com/dacharyc/braintrust-code-gen-experiment

Braintrust is the eval platform that a sister team, the MongoDB Education AI team, uses to perform evals for a variety of projects. I decided here to try to eval a few different cases to figure out what the lift was for each scenario:

- What can the LLM produce unassisted?
- What can the LLM produce if you hand it a relevant docs page?
- What can the LLM produce if you hand it a skill?

For my first attempt at evals, it was actually pretty cool. I had a multi-scorer process:

- A syntax check to confirm that the code was valid in that language
- A semantic check to determine whether the generated result contained "must-have" patterns or "must-not-have" antipatterns
- An execution check that attempted to actually run the code and confirm it produced the correct result

And what I discovered?

Oh no, friends. The skill did *not* help. In fact, the LLM successfully produced the correct code in the unassisted case across many runs, but having the Skill in context, specifically, caused it to produce incorrect output 66% of the time.

Yes, you're reading that right. My skill, generated *programmatically* from correct, valid documentation - no LLM generation involved, just human-written correct words being excerpted and moved around to present in LLM-friendly ways - this lovingly-programmatically-crafted skill made the output *worse*.

So I had to learn more. If my skill was making the output *worse* - why? What did it take to actually make the output better?

This prompted the deep dive I did in the [Agent Skill Report](https://agentskillreport.com). I looked at 673 skills across the ecosystem to try to figure out what signal I could find from each skill, and whether that could tell me anything about whether a skill would help an agent perform a task or not.

As part of that project, I selected 20 skills most likely to tell me interesting things about skills; designed to test some hypotheses I had about where things might go wrong, and why; and performed evals with those skills. This eval system did not use Braintrust; I did this project all on my own, outside of work hours, spending my own money on Anthropic API credits (around $180) and a *lot* of hours over a long weekend and a few weeknights the following week.

It was a bit more sophisticated. For the interested, the eval pipeline for this larger analysis project is here: https://github.com/dacharyc/agent-skill-analysis/tree/main/eval

This system tested across four scenarios to detect lift:

- Baseline unassisted
- With Skill (skill + any relevant reference files)
- Skill only (no reference files, designed to test for a specific type of failure case)
- Realistic context (skill, a simulated system prompt and user message, and simulated codebase context for grounding)

And then I didn't just run a few random prompts that I thought might exercise the skill. I designed tasks specifically to test for cross-language contamination, which is what I thought I was observing in the skill-induced degradation in LLM output in my MongoDB docs skill test project:

Each skill has 5 tasks probing different contamination vectors:

| Task Type | What It Tests |
|-----------|---------------|
| `direct_target` | Primary language of the skill (should benefit or be neutral) |
| `cross_language` | Same domain, different language (tests bleed) |
| `similar_syntax` | Syntactically similar language (highest PLC risk) |
| `grounded` | Includes existing code context (tests contamination with grounding) |
| `adjacent_domain` | Related task (tests scope bleed) |

I ran each condition 3 times at a temperature of 0.3, to introduce *some* opportunity for variance within what I felt was a reasonable range for coding agent tools.

What I *hoped* to find was that the skills where I expected to see a decline in output quality showed a decline in output quality. This would tell me that I had successfully predicted from my analysis what the failure modes would be, and could avoid them in designing our official company skills.

What I *actually* found was two things:

1. Some of the skills *did* fail in very interesting ways, but it was more surprising and less predictable to me than my initial hypotheses suggested it would be.
2. In putting together the tasks and the patterns/anti-patterns, agents and I visited a *lot* of documentation pages. And this is the project where I got to watch agents fail spectacularly to read docs in a variety of ways. I had Claude Code start keeping track of the [failures and successful patterns here](https://github.com/dacharyc/agent-skill-analysis/blob/main/eval/pattern-artifacts/claude_context.md), and it became the basis for the testing I did for the [Agent-Friendly Docs article](https://dacharycarey.com/2026/02/18/agent-friendly-docs/) that kicked off the [Agent-Friendly Documentation Spec](https://agentdocsspec.com) and the [afdocs tool](https://afdocs.dev).

If you want to read more about the specifics, take a look at [the behavioral section](https://agentskillreport.com/#behavioral) of the Agent Skill Report. I observed six distinct content interference mechanisms, where having the skill in context made the LLM output *worse* in specific ways.

So I knew two things:

- Skills making output worse wasn't a problem isolated to my attempt at programmatically generating skill files from the MongoDB documentation
- There are *lots* of ways a Skill can go wrong, and most of them will be difficult to detect for an average user

## How to mitigate the problems

"So Dachary," I hear you asking, "how do I actually make sure my skill *helps* output instead of making it worse?"

Well, friends, I wish I had a simple "do this thing" answer for you, but I don't.

Or I do, but "simple" probably isn't the right word.

You have to test these things. But like, *really* test them. Not *just* in the [Anthropic `skill-creator` eval](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) sense, although it's great as a starting point if you don't have one. But you need to *really* test the no skill vs. with skill lift, with realistic context, across a variety of conditions and tasks, to find out how the skill performs in practice.

And then you need to do it again across every model family that your customers might be using. So you can't use just Claude Sonnet. You probably need to test across Claude Opus, GPT, Gemini... and you can't use the cheap models, because your customers probably aren't using the cheap models. You need to use the real models your customers are using.

Ideally, you should *also* test them using the agent harnesses your customers are using. Claude Code. Codex. Cursor. Wherever you think your users are running your skills, you need to be testing them. They all added support for Agent Skills *before* Anthropic provided an implementation guide, so there is no guarantee that they all support the skills the same way, or that the skills behave the same across platforms even if they use models where the skills provide good lift.

And then you need to *keep* testing them. The product that the skills are written against will change. That's what products do. The models *also* change. Model providers are constantly pushing for better, more accurate, faster, cheaper; and those model changes affect how your skill performs. And finally, the platforms themselves continue to evolve. So you need to test across platform changes.

It sounds like a lot of work, because it is.

## Don't forget maintenance

Oh, but testing isn't the end of the road. You also need to maintain the skills. Do user testing. Explore more customer needs. Update the skills as your products change. Update your skills as the Agent Skills *spec* changes - it's an unversioned spec and things keep getting tweaked.

And also, if you have open issues on a repo where you publish your skills, read them and actually do something about them.

At the time of this writing, [Supabase has four open issues](https://github.com/supabase/agent-skills/issues) reporting problems with their skills. Two of them are bugs that have been open for two months (opened March 10, still open May 8). A Supabase engineer gave a talk recently that was republished to YouTube: [Skill Issue: How We Used AI to Make Agents Actually Good at Supabase — Pedro Rodrigues, Supabase](https://youtu.be/GmAQKINjv1E). It sounds like Supabase attempted to do the testing part. But testing it once while you're developing it isn't the end of the story. You need to actually monitor repos and apply updates and bugfixes once you publish them.

Hey, this is starting to sound a lot like docs or libraries, isn't it?

## Skills are not one-and-done - what does the skill lifecycle look like?

Like other resources, Agent Skills have a lifecycle. I've been ideating around what this looks like, and have started building some tooling for Agent Skill lifecycle management at MongoDB. I'll definitely be writing more about what this looks like as we start rolling out new processes and tooling, so stay tuned!
