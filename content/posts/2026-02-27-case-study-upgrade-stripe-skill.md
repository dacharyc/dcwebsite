---
title: Case Study - 'upgrade-stripe' Agent Skill
author: Dachary Carey
layout: post
description: In which I deep dive on a Stripe Skill, and what it means for the industry.
date: 2026-02-27 08:00:00 -0500
url: /2026/02/27/case-study-upgrade-stripe-skill/
image: /images/case-study-upgrade-stripe-skill-hero.jpg
tags: [Documentation]
draft: false
---

When I did the research for my recent [Agent Skill research paper](https://agentskillreport.com), I took two approaches to trying to understand the impact of Skills in developer workflows: take a wide, zoomed-out look at Skills across different segments, verticals, and use cases, and take a closer look at a subset of those Skills where I had interesting theories I wanted to probe more closely. One of the skills I took a closer look at was the [`upgrade-stripe`](https://github.com/stripe/ai/blob/main/skills/upgrade-stripe/SKILL.md) Skill. I had ideas about what I might find, and thought it might have implications for how other companies with multiple programming language SDKs should approach Skill development. What I found was not what I expected, but was arguably more interesting.

## What's in the Skill

The `upgrade-stripe` Skill is a guide for upgrading Stripe API versions and SDKs. It's about 1,300 tokens, which makes it one of the smallest Skills I evaluated. It covers:

- How Stripe's date-based API versioning works (e.g. `2026-01-28.clover`, `2024-12-18.acacia`)
- The difference between backward-compatible and breaking changes
- How to set the API version globally and per-request
- Version handling differences between dynamically-typed SDKs (Python, Ruby, Node.js) and strongly-typed SDKs (Go, Java, .NET)
- Stripe.js versioning and how it pairs with API versions
- Mobile SDK versioning
- A step-by-step upgrade checklist

It includes code examples in Python, Ruby, and JavaScript showing how to pin the API version. It also explicitly notes that strongly-typed languages like Go use a fixed version matching the SDK release, and you should *not* try to set the API version manually.

If you're a human developer doing a Stripe version upgrade, this is exactly the kind of document you'd want. It explains the versioning system, gives you the patterns for each language, and walks you through the process. It's well-organized, concise, and covers the important gotchas. The idea of packaging this as a Skill so an agent can help with your upgrade is genuinely sensible.

## What I expected

One of the main reasons I took a look at `upgrade-stripe` was because Stripe provides SDKs in multiple programming languages. There is a phenomenon that LLM researchers have observed, which they term Programming Language Confusion (PLC). The idea is that if you show an LLM code in a semantically similar programming language, it can cause the LLM to output syntactically incorrect code in a different language. For example, if you give an LLM a Python code example and ask it to output JavaScript, it may output syntactically incorrect JavaScript that has some Python stuff mixed in. If you ask it to output JavaScript _without_ showing it Python first, it can correctly output JavaScript. If you'd like to learn more about the research, check out this paper that names and formalizes the PLC phenomenon, systematically evaluating it across 10 LLMs and 16 programming languages: "[Evaluating Programming Language Confusion](https://arxiv.org/abs/2503.13620)" (SANER 2026).

I took a closer look at 10 Skills that I identified as having a higher risk to exhibit PLC in practice. The Stripe Skill was on the list. I was hoping to learn whether this was an issue in real-world usage scenarios, and to help decide whether we need to provide language-specific Skills for multi-language products or if realistic context mitigates the effect in practice.

## How I tested it

A structural analysis can tell you what's *in* a Skill (how many tokens, what languages, how the content is organized), but it can't tell you what a Skill actually *does* to the model's output. For that, you need a behavioral evaluation: give the model real tasks with and without the Skill loaded, and compare the results. Does the Skill help? Hurt? Change the output in unexpected ways?

For the behavioral evaluation in the paper, I designed five tasks for each Skill I tested. These tasks weren't random; they were designed to probe specific interference categories:

- **Direct target**: A task in the Skill's primary language. For `upgrade-stripe`, this was writing a Python module that initializes a Stripe client, creates a customer, and sets up a subscription.
- **Cross-language**: The same domain, but a different language the Skill doesn't provide examples for. Here, that was a Go function to retrieve and update a customer.
- **Similar syntax**: A language that's syntactically close to one in the Skill. I used a Node.js webhook middleware task, since JavaScript examples appear in the Skill.
- **Grounded**: A task that provides existing code to modify, rather than generating from scratch. I gave the model a real Python payment integration and asked it to upgrade from the old API version to the new one.
- **Adjacent domain**: A related but different task in a language from the Skill. A Ruby webhook handler for subscription lifecycle events.

I ran each task under three conditions:

1. **Baseline (A)**: Just the task prompt, no Skill loaded. This is the control. How well does the model do on its own?
2. **With Skill (B)**: The task prompt plus the full SKILL.md injected as context. This tests the Skill in isolation.
3. **Realistic context (D)**: The Skill plus a condensed version of a real agentic system prompt and simulated codebase context. This is the closest approximation to how a developer would actually use the Skill in practice, with all the other context an agent normally has.

Each condition was run three times at temperature 0.3 to check for consistency. Generation used one model (Sonnet), and judging used a different model (Opus) to avoid self-preference bias. The judge scored each output on four dimensions: language correctness, API idiomaticity, functional correctness, and code quality. I also used deterministic pattern matching to check for specific expected API calls and anti-patterns (like Ruby syntax showing up in Go output).

## What I found

Here's where it gets interesting. The `upgrade-stripe` Skill had the *highest* structural contamination score in my entire dataset: 0.93 out of 1.0. That score measures the risk of cross-language contamination based on the presence of code examples in multiple languages for the same API. By every structural indicator, this Skill should have been a poster child for Programming Language Confusion.

It wasn't.

The model didn't mix Python syntax into Go output, or Ruby method names into JavaScript. The languages stayed clean. But something else happened that I didn't anticipate.

**The model started making things up.**

Not random hallucinations. Plausible, thematically consistent fabrications that *sounded* like they should be real Stripe API calls. Things like:

- `params.SetStripeVersion()` in Go (an invented method that feels like it should exist given the Skill's emphasis on version management)
- Non-existent Go constants like `stripe.ErrorTypeConnection`
- Misapplied error handling patterns where the model used the right class names but wrong calling conventions
- `stripe-go/v81` (a real but outdated version that doesn't correspond to the API version the Skill targets; the correct version is v84)

See the pattern? The Skill teaches extensively about API versioning, migration patterns, and per-request version overrides. The model appeared to internalize the *concepts* and then construct API calls that "should" exist based on those concepts. It wasn't confused about languages. It was confidently filling in gaps.

### The numbers

The Skill-only condition (B) showed a modest mean degradation of -0.117 compared to baseline. Not statistically significant (p=0.108). You could look at that and think: fine, the Skill doesn't really hurt.

But the realistic context condition (D) told a different story. The mean degradation jumped to -0.383. Realistic context made things *worse*, not better. This was the opposite of the pattern I saw in most other Skills, where adding realistic context (the system prompt, codebase snippets, conversation history) actually mitigated whatever interference the Skill introduced.

Here's the per-task breakdown:

| Task | Type | Language | Skill-only (B-A) | Realistic (D-A) |
|------|------|----------|-------------------|------------------|
| 01 | Direct target | Python | 0.000 | -0.250 |
| 02 | Cross-language | Go | -0.167 | -0.666 |
| 03 | Similar syntax | JavaScript | -0.167 | -0.334 |
| 04 | Grounded | Python | +0.167 | 0.000 |
| 05 | Adjacent domain | Ruby | -0.417 | -0.667 |

Two things stand out. First, the grounded task (04) was essentially immune. When the model had actual code to modify, it stayed anchored to what was in front of it and didn't try to get creative with version management patterns. Second, every *ungrounded* task got worse under realistic context.

My working theory for why realistic context amplified the problem: the richer system prompt that comes with an agentic workflow may reinforce the model's tendency to be "helpful" by elaborating. The Skill gives it conceptual scaffolding about Stripe's versioning system, and the system prompt's emphasis on being thorough and helpful may give it permission to run with that scaffolding. The result is more elaborate, more confident, and more wrong. This is one possible explanation; the interaction between Skill content and system prompts is an area that warrants more research.

I tracked contamination signals across conditions to quantify the escalation pattern. Baseline produced 1 contamination signal total. With the Skill loaded, that jumped to 12. Under realistic context, it hit 28. The correlation is striking: more context correlated with more fabrication signals, suggesting the Skill was giving the model just enough to be dangerous.

## What I did next

The findings pointed toward what I started calling the "partial knowledge hypothesis." The idea is this: skills that teach API *vocabulary* (naming patterns, migration strategies, conceptual frameworks) without providing API *ground truth* (complete method signatures, valid version numbers, exhaustive error class hierarchies) may create a gap. The model picks up enough to construct plausible-sounding API calls, but not enough to construct *correct* ones, and fills the gap with confident fabrication.

Researchers sometimes call this gap-filling behavior "[confabulation](https://arxiv.org/abs/2406.04175)," borrowing the term from neuroscience: the model isn't lying, it's constructing plausible narratives to bridge what it doesn't know. There's also a growing body of work on [knowledge conflicts](https://aclanthology.org/2024.emnlp-main.486/) between what an LLM learned during training (parametric knowledge) and what it's given in context. What I observed fits that frame: the Skill's vocabulary conflicted with the model's stale parametric knowledge of the Stripe API, and the model resolved the conflict by blending the two in ways that produced plausible but incorrect output.

If that hypothesis was right, then providing more complete API reference information alongside the Skill should reduce the fabrication. So I created two synthetic variants of the Skill to test it.

Both variants used the *identical* SKILL.md. The only difference was what reference files I included alongside it.

### The targeted variant

The first variant added a single quick-reference file (about 2,000 tokens) that provided minimal ground truth: correct SDK version numbers, client initialization patterns, error class hierarchies, and webhook verification signatures across Python, Go, Node.js, and Ruby. Just the facts. No explanatory prose, no extended examples. Here's the kind of thing it contained:

```
## Current Versions
| SDK          | Version  | API Version          |
|--------------|----------|----------------------|
| stripe-python| 14.3.0   | 2026-01-28.clover    |
| stripe-go    | v84.3.0  | Fixed by SDK release  |
| stripe-node  | 17.x     | 2026-01-28.clover    |
| stripe-ruby  | 13.x     | 2026-01-28.clover    |
```

Correct version numbers. Correct method signatures. Correct error classes. The things the model was getting wrong.

### The comprehensive variant

The second variant went bigger: four per-language reference files totaling about 8,600 tokens. Full SDK documentation covering pagination, object expansion, idempotency keys, metadata handling, retry patterns, and complete webhook handlers. If the targeted variant was "just the facts," this was "the whole manual."

### Results

On the original five evaluation tasks, the targeted variant worked beautifully. It eliminated the mean Skill-only degradation entirely (B-A went from -0.117 to +0.000) and reduced realistic-context degradation by 88% (D-A went from -0.31 to -0.04). The fabricated version numbers and error patterns largely disappeared, consistent with the model having the correct ones right there in context.

The comprehensive variant, on the other hand, made things *worse* than the original Skill. Both the B-A and D-A deltas came in at -0.15. The judge flagged a new problem: cross-SDK pattern leakage. With four languages' worth of full API documentation in context, the model started adopting conventions from the wrong language. Python code picked up Node.js-style parameter passing patterns.

There's an irony here. I started this investigation expecting to find Programming Language Confusion in the original Skill, and didn't. The languages stayed syntactically clean. But by loading comprehensive multi-language references into context, I inadvertently *created* the conditions for PLC. The cross-SDK leakage isn't syntactic confusion (the model isn't writing `def` in a Go file), but it's the same phenomenon operating at the API idiom level: correct syntax, wrong language-specific conventions. The PLC research focuses on syntax-level confusion between similar languages; this is that same confusion happening one layer up, at the level of SDK calling patterns.

One dimension tells this story particularly clearly. When I looked at just the `api_idiomaticity` scores (how well the generated code follows the conventions of its target language), more cross-language reference material correlated with worse idiomaticity: mean B-A of -0.17 with no references, -0.33 with the targeted reference, and -0.58 with comprehensive references. The targeted variant's *overall* composite score was still a net improvement (its gains in correctness more than compensated), but the idiomaticity dimension reveals the tradeoff: providing multi-language references in a single file gives the model more opportunities to mix up language-specific conventions.

So the targeted approach was the clear winner. Except for one problem.

### The out-of-band test

I added three more tasks that exercised Stripe API surfaces *not* covered by any reference file: PaymentIntent creation and refund handling, Connect Express account onboarding, and usage-based billing meter events. These were all real Stripe APIs, but the targeted reference file didn't include signatures or patterns for any of them.

The results were unambiguous. On the original five tasks (where the reference file covered the relevant APIs), the targeted variant scored a mean B-A of +0.000. On the out-of-band tasks, it scored -0.500. The protective effect was entirely local to the API surfaces the reference file covered.

And it got more nuanced than that. Rather than pure fabrication (inventing APIs from scratch, like the original Skill caused), the model with the targeted reference exhibited a new failure mode I started calling "hybridization." It would adopt the new API vocabulary from the reference file (correct class names like `StripeClient`, correct method patterns like `v1.customers.create`) but then fill in procedural details from its pretrained knowledge of the *older* API. The result was chimeric code: correct structure, incorrect calling conventions. New names, old behavior. This is a textbook example of the knowledge conflict problem: the model's parametric memory says the Stripe API works one way, the in-context reference says it works another, and the model splits the difference rather than fully deferring to either source.

## What it means for Agent Skill development

The `upgrade-stripe` case study crystallized something I kept seeing across the broader research: what a human developer needs from documentation is not the same as what an agent needs.

A human developer doing a Stripe version upgrade has years of context. They know roughly what the Stripe API looks like. They know what error classes exist. At the time of writing, they know that Go SDK versions are in the v80s, not the v40s or v200s. When they read a migration guide that says "pin to the new version and update your error handling," they can fill in the specifics from experience. The delta between old and new is all they need.

An agent doesn't have that. It has whatever's in its training data (which may be stale) and whatever's in its current context. When you give it a Skill that teaches *concepts* about API versioning without grounding those concepts in concrete API facts, the model does what LLMs do: it generates plausible completions. And plausible completions of API migration guides are API calls that sound right but may not exist.

This is probably not a problem unique to Stripe. Any company with multiple programming language SDKs, a versioned API, and a migration guide should consider whether they face a similar risk. The more conceptual the Skill (patterns, strategies, best practices), the more opportunity the model has to fill in gaps with fabrication.

### Practical takeaways

**Test your Skills with evals, not vibes.** The `upgrade-stripe` Skill looked good to a human reader. It was well-organized, concise, and covered the right topics. The structural contamination analysis flagged it as high-risk, but for the wrong reason (it predicted cross-language confusion, which didn't happen). The only way to discover the actual failure mode was to run tasks under controlled conditions and compare outputs. If you're publishing a Skill, build a small eval suite: a handful of tasks in each language your Skill covers, run under baseline and with-Skill conditions, with a judge (human or LLM) scoring the output.

**Test with realistic context, not just the Skill in isolation.** The Skill-only condition showed modest degradation. The realistic-context condition showed three times as much. If I had only tested the Skill in isolation, I would have concluded it was basically fine. The interaction between the Skill and the broader agentic context was where the real problems emerged.

**Favor targeted ground truth over comprehensive documentation.** A 2,000-token quick reference with correct version numbers, method signatures, and error classes outperformed an 8,600-token comprehensive reference. More documentation is not better when it introduces cross-language confusion opportunities. Include the facts the model is most likely to get wrong, and leave out the rest.

**But know that targeted ground truth has limits.** The reference file only protected the specific API surfaces it covered. Out-of-band tasks showed *worse* performance than the original Skill. If your API surface is large, you'll need to decide what to cover and accept that the reference can't protect against everything.

**Ground your users in real code.** The grounded task (which provided actual code to modify) was the one task that was completely immune to both the Skill's interference and the realistic-context amplification. When the model has concrete code in front of it, it stays anchored. This is a design insight for how developers use agent Skills in practice: if your Skill helps with migration, encourage users to provide their existing code rather than asking the agent to write from scratch.

**Be precise about when your Skill should trigger.** If a Skill can make outputs worse when it's loaded, then accidentally triggering it on the wrong task is actively harmful. Agents typically decide whether to load a Skill based on its name and description alone. That makes the description a gatekeeper: a vague or keyword-stuffed description that matches too broadly means the Skill gets loaded into contexts where it doesn't belong, injecting partial knowledge that can prime fabrication. Write the description to match the narrow set of tasks where the Skill genuinely helps. "Guide for upgrading Stripe API versions" is better than "Stripe API reference for version management, migration, SDK setup, error handling, and webhook configuration." The tighter the trigger, the less chance the Skill gets loaded where it can do harm.

**Don't assume structural analysis catches the real risks.** The `upgrade-stripe` Skill had the highest structural contamination score in my dataset (0.93), but the actual failure mode (API hallucination) was completely orthogonal to what that score measures. Meanwhile, a Skill with a structural score of 0.07 (`react-native-best-practices`) produced one of the *largest* degradations in my sample. Structural analysis can flag *potential* risks, but behavioral testing is the only way to find *actual* ones.

These findings aren't just about Stripe. They're about a fundamental mismatch between how we think about documentation (as information for humans to interpret) and how agents actually use it (as patterns to extend and complete). As more companies publish Skills for their products, understanding this gap will be the difference between Skills that genuinely help developers and Skills that make agents more confidently wrong.
