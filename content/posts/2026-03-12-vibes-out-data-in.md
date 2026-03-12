---
title: Vibes are Out, Data is In
author: Dachary Carey
layout: post
description: In which I explain why a vibes-based approach to AI and docs ain't cutting it.
date: 2026-03-12 07:30:00 -0500
url: /2026/03/12/vibes-out-data-in/
image: /images/vibes-out-data-in-hero.jpg
tags: [ai, documentation]
draft: false
---

There's a growing body of advice about how to write documentation for AI. Some of it is grounded in real observations. A lot of it is grounded in vibes: intuitions extrapolated from LLM research that was never designed to test documentation, recommendations passed around conference talks and blog posts until they calcify into "best practices," and vendor guidance that may or may not reflect how agents actually behave in the wild. People aren't wrong to form theories. But we're skipping the step where we test those theories in the context that actually matters, and then building an industry playbook on top of the untested assumptions.

I've spent the last several months researching how agents interact with documentation, from [observing real-time agent access patterns](https://dacharycarey.com/2026/02/18/agent-friendly-docs/) to [analyzing 673 agent skills](https://agentskillreport.com) with structural, content, and behavioral evaluations. What I've found keeps challenging the conventional wisdom. Not because the conventional wisdom is always wrong, but because the story is more complicated than "follow these five rules and your docs will be AI-ready."

## Apples to Oranges in LLM Research

A few research papers come up repeatedly in conversations about AI-friendly documentation. The most commonly cited is probably ["Lost in the Middle"](https://arxiv.org/abs/2307.03172) (Liu et al., 2023), which found that LLMs pay the most attention to information at the beginning and end of their context window, with significantly less attention to information in the middle. It's a real finding, replicated across multiple models. And the implication people draw for docs is intuitive: put important information at the top and bottom of your pages, because the LLM might miss what's in the middle.

But - that paper tested LLMs on multi-document question answering and key-value retrieval tasks. The "documents" were short passages, and the task was to find a specific answer buried among distractors. That's a different scenario from an agent reading a documentation page to understand how to use an API.

When an agent fetches your docs page, it's not searching for a needle in a haystack of unrelated passages. It's reading a structured document with headings, code examples, and logical flow, all about a single topic it deliberately sought out. The positional attention effects from "Lost in the Middle" may still apply in some form, but the magnitude, the practical consequences, and the right mitigation strategies could all be different in a documentation context. We don't know, because nobody has run that experiment.

The same applies to other commonly cited research. ["DETAIL Matters"](https://arxiv.org/html/2512.02246v1) found that detailed prompts improve accuracy on procedural tasks but can constrain reasoning on open-ended tasks. That's useful for thinking about how developers write prompts, but it doesn't tell us how the level of detail in our *documentation* affects agent task performance. Research on [sycophancy in LLMs](https://aclanthology.org/2025.findings-emnlp.121.pdf) shows that detailed user context amplifies compliance tendencies. Interesting for understanding agent chat dynamics, but what does it mean for how an agent processes a migration guide?

These papers help us form hypotheses. They give us a theoretical basis for thinking about how LLMs process text. But there's a gap between "LLMs show reduced attention to middle-positioned content in multi-document QA" and "you should restructure your docs pages so the important stuff isn't in the middle." That gap is the absence of documentation-specific testing.

Research on [Programming Language Confusion](https://arxiv.org/abs/2503.13620) (PLC) provides another concrete example. The finding is real: showing an LLM code in one language can cause it to produce syntactically incorrect code in a different language. The natural recommendation for documentation is to separate code examples by language, or to avoid showing multiple languages on the same page. Sounds reasonable.

But when I tested this with the `upgrade-stripe` skill in my [agent skill analysis](https://agentskillreport.com), the skill had the *highest* structural contamination score in my dataset (0.93 out of 1.0) because it contained code examples in Python, Ruby, and JavaScript for the same API. Every structural indicator said this should cause Programming Language Confusion. It didn't. The languages stayed syntactically clean. Instead, the model started fabricating plausible but nonexistent API calls, a completely different failure mode that the PLC research didn't predict and that structural analysis couldn't detect.

That's not a knock on the PLC research. It's a knock on the practice of taking research findings from one domain and applying them as best practices in another without testing. The research told a true story about one kind of risk. Reality presented a different risk entirely. And if I'd relied on the research alone, I would have spent my time mitigating a problem that didn't exist while missing the problem that did.

## Agents Add a New Layer

Most of the "AI-friendly docs" conversation treats AI as a monolith. But as I explored in [An Agent is More Than Its Brain](https://dacharycarey.com/2026/03/02/an-agent-is-more-than-its-brain/), a coding agent is not just an LLM reading your docs page. It's an LLM wrapped in a harness with tools, system prompts, persistent context files, conversation history, and permission systems. All of those components affect how the agent processes your documentation.

When an agent reads your docs, it's doing so within a rich context that includes its system prompt (which shapes its behavior and priorities), any skill files or project instructions it has loaded (CLAUDE.md, AGENTS.md, copilot-instructions.md), the developer's conversation history (which tells the agent what it's trying to accomplish), and whatever other context it has accumulated during the session. Your docs page is one input among many, and the interaction between your content and all that surrounding context can produce effects that testing the LLM in isolation would never reveal.

This matters because agents don't just *recommend* code to developers who filter the output through their own expertise. Increasingly, agents make direct changes to codebases. A developer using Claude Code or Cursor might review the diff, but they're trusting the agent's output more than they would a chatbot suggestion. If the agent's understanding of your API is subtly wrong because of how your documentation interacted with its other context, that wrong understanding gets committed to a codebase. The stakes are higher than they were when AI was primarily an answer engine.

My research on the `upgrade-stripe` skill illustrates this vividly. When I tested the skill in isolation (just the skill content plus a task prompt), it showed modest degradation: a mean score change of -0.117 compared to baseline. You could look at that and think it's basically fine. But when I tested it with realistic agentic context (a system prompt, simulated codebase snippets, conversation history) the degradation jumped to -0.383. Realistic context made the problem *three times worse*, the opposite of what I saw with most other skills, where realistic context actually mitigated interference. The interaction between the skill's content and the broader agentic environment was where the real problem lived.

If you're testing your documentation's effect on LLM outputs by pasting your docs into a chat window and asking the model to generate code, you're testing a scenario that doesn't match how agents actually use your content. The results might be misleading in either direction: they might overstate problems that realistic context would mitigate, or they might completely miss problems that only emerge when the full agentic context is in play.

## Why "We Can't Know" is a Myth that Keeps You Weak

I hear a version of this constantly: "We can't really know how these models work, so we just have to do our best and hope." Sometimes it's phrased as frustration with model providers who won't share implementation details. Sometimes it's a general resignation about the opacity of neural networks. Either way, it's used as a reason to stop investigating and start guessing.

It's true that we can't fully reverse-engineer the internal mechanisms of a large language model. It's true that model providers don't document every detail of how their agent platforms process web content (as I discovered [firsthand](https://dacharycarey.com/2026/02/19/agent-web-fetch-spelunking/) when trying to understand Claude's web fetch pipeline). But "we can't know the implementation" is not the same as "we can't know the behavior." These are different claims, and conflating them keeps documentation teams from doing the work that would actually inform their decisions.

You can observe how agents behave with your documentation right now. You can measure what content they see, what gets truncated, what gets misinterpreted, and what produces correct outputs versus incorrect ones. You can run controlled experiments comparing agent performance with and without specific documentation changes. You don't need to understand the attention mechanism of a transformer to measure whether restructuring a docs page improves agent task completion rates.

When I tested [how different agent platforms access web content](https://dacharycarey.com/2026/03/05/how-to-measure-agent-web-traffic/), I didn't need anyone to hand me implementation specs. I watched agent behavior in real time. I checked server logs. I compared what different platforms did with the same content. The information was there for the taking; it just required someone to go look.

The "we can't know" framing has a secondary cost: it makes documentation teams dependent on whoever *does* claim to know. If you believe you can't figure this out yourself, you're reliant on model providers, platform vendors, and consultants to tell you what to do. Some of those sources have genuine expertise. Some have products to sell. And without your own observations to compare against, you can't tell the difference.

The documentation community has a long history of user research, analytics, and evidence-based decision making. We know how to observe users, measure outcomes, and iterate based on data. The tools we need for this new challenge are tools we already have. What's new is the user: instead of (or in addition to) a human developer, it's an agent. The methodology transfers. We just need to apply it.

## What Happens if You Rely on Vibes

Let me get specific about what can go wrong when vibes-based recommendations aren't tested in context.

### "Put important information at the top"

This one sounds unobjectionable. It's based on the "Lost in the Middle" finding, and the logic seems clear: if LLMs pay less attention to middle-positioned content, front-load what matters.

But here's what I actually observed when watching agents use documentation pages: the biggest factor in whether an agent sees your content isn't where it falls on the page. It's whether the page fits within the agent's truncation limits at all. A page that's 400K characters of HTML means the agent might see 25% of it, regardless of how you ordered the sections. Meanwhile, a focused 5K-character markdown page gets read in full. Source order matters, but it matters a lot less than page size and content density, factors that "Lost in the Middle" doesn't address because it wasn't studying web-fetched documentation.

More importantly, what goes "at the top" for an agent isn't always what you think. I discovered that on many docs sites, the [actual content doesn't start until 55-98% into the converted HTML](https://dacharycarey.com/2026/03/01/make-hugo-site-agent-friendly/) because of navigation chrome, CSS, JavaScript, and sidebars. You can restructure your content sections all you want, but if the agent's truncation window fills up with your site's CSS before it reaches your first heading, the ordering of your *content* sections is irrelevant.

### "Show negative examples"

My agent skill analysis found that 60% of top-scoring skills include anti-patterns sections and 50% include "When NOT to Use" guidance; both were absent from every bottom-scoring skill. So negative examples seem to help with skill quality. That's a data point.

But it's a data point about *skills*, a specific format where anti-patterns serve as decision boundaries for when and how to apply the skill. Does the same pattern hold for documentation pages that agents fetch via the web? I haven't tested that. And the dynamics could be very different. A skill is loaded into the agent's context deliberately, as instructions the agent is supposed to follow. A docs page is fetched as reference material the agent is supposed to extract information from. Anti-patterns might help in one context and be noise in the other. Or they might help in both. The point is: we don't know, and treating an observation from one context as a recommendation for a different context is exactly the vibes-based reasoning I'm cautioning against.

### "More documentation is better"

When I tested the `upgrade-stripe` skill with different amounts of reference documentation, the results directly contradicted this intuition. A targeted 2,000-token quick reference with correct version numbers and method signatures eliminated the skill's degradation entirely. But an 8,600-token comprehensive reference covering the full SDK documentation made things *worse* than the original skill. The comprehensive reference introduced cross-SDK pattern leakage, where conventions from one language's SDK started appearing in code generated for a different language. More documentation gave the model more opportunities to mix things up.

There's an even more sobering finding. The targeted reference only protected the specific API surfaces it covered. When I tested tasks that exercised Stripe APIs *not* covered by the reference file, performance dropped to -0.500, worse than the original skill without any reference. The model had adopted vocabulary from the reference (correct class names, correct method patterns) but filled in procedural details from its stale training data, producing chimeric code with new names but old behavior. More documentation helped where it was precise, but the protection didn't generalize.

### "LLMs lose information in the middle of long pages"

I keep coming back to this one because it's a recommendation I hear often, and it's one where the gap between the research and the real-world documentation context is widest.

Here's what I've actually observed about agents and long pages. When my agent fetched a [427,000-character docs page](https://dacharycarey.com/2026/02/18/agent-friendly-docs/), its tool truncated the content at roughly 150,000 characters. The agent saw barely a third of the page. It didn't "lose information in the middle." It never saw two-thirds of the page *at all*. The truncation was mechanical, not attentional. A Python developer asking about that page got the mongosh version of the tutorial because that's what came first in the source order, not because the model lost track of the Python section somewhere in the middle.

The practical problem isn't a U-shaped attention curve. It's a cliff: content past the truncation limit doesn't exist as far as the agent is concerned. No amount of restructuring within the page changes that. The fix isn't to move important content to the beginning and end of a long page. The fix is to make the page shorter, or to serve a focused version that only contains what the agent needs.

### The real risk: untested best practices making agents worse

All of the above examples share a common thread: recommendations that sound reasonable based on LLM research but that don't match what happens when agents actually interact with documentation. The recommendations aren't necessarily wrong. Some of them probably help in some contexts. But without testing them in the specific context of agent-consumed documentation, we're building on assumptions.

And assumptions can actively hurt. The `upgrade-stripe` case showed that a skill designed with every good intention (well-organized, concise, covering the right topics) was silently making agents produce worse code. The structural analysis flagged it as high-risk for the wrong reason: it predicted cross-language confusion, which didn't happen. The actual failure mode (API fabrication amplified by realistic context) was invisible to every analysis method except behavioral testing against real tasks.

If that can happen with a 1,300-token skill, imagine what can happen with the documentation changes an entire industry is making based on untested recommendations. We might be restructuring pages in ways that help in one dimension but hurt in another we haven't measured. We might be adding content that makes agents more confidently wrong. We might be optimizing for a research finding that doesn't apply in our context while ignoring problems that do.

## What We Should Do Instead

I'm not saying throw out all existing guidance. I'm saying test it. The documentation community knows how to do this. We've been running A/B tests, analyzing user behavior, and measuring task completion for decades. The agents are new. The methodology isn't.

Here's what I think the industry needs:

**Test recommendations in documentation context.** If you're going to tell docs teams to front-load important information, show that this improves agent task performance on actual docs pages, not just that LLMs have positional attention biases in multi-document QA benchmarks. If you're recommending negative examples, show that agents produce better code when the docs include anti-patterns versus when they don't.

**Test with realistic agentic context, not just isolated LLM interactions.** Pasting your docs into a chat window and asking the model to write code is not how agents use your docs. Agents have system prompts, tool implementations, conversation history, and persistent context that all interact with your content. Testing in isolation can miss problems that are three times worse in practice, or overstate problems that realistic context mitigates.

**Measure task performance, not just content quality.** A docs page can score well on every readability and structure metric and still cause agents to produce incorrect code. The only way to know whether your documentation changes actually improve outcomes is to measure agent task performance: give the agent a real task, give it your docs, and evaluate whether the generated code is correct, idiomatic, and functional.

**Share your findings.** I've been publishing everything I learn, from [web fetch internals](https://dacharycarey.com/2026/02/19/agent-web-fetch-spelunking/) to [agent traffic measurement](https://dacharycarey.com/2026/03/05/how-to-measure-agent-web-traffic/) to [full behavioral evaluation results](https://agentskillreport.com), because this is a problem the entire industry faces. The more people testing and sharing observations, the faster we get from vibes to evidence.

**Be skeptical of universal rules.** My research keeps showing that context matters more than any single variable. Realistic agentic context mitigated interference in most skills but amplified it in others. Comprehensive reference documentation helped in some dimensions but hurt in others. Targeted documentation protected the surfaces it covered but made uncovered surfaces worse. There may not be universal rules. There might only be tradeoffs that you need to understand for your specific docs.

We're at an inflection point. The choices documentation teams make in the next year or two will shape how agents interact with technical content for a long time. Let's make those choices based on evidence, not vibes.
