---
title: Confident-Sounding Gibberish
author: Dachary Carey
layout: post
description: In which I share research about why LLM-generated output is hard to fact check.
date: 2026-03-30 07:00:00 -0500
url: /2026/03/30/confident-sounding-gibberish/
image: /images/confident-sounding-gibberish-hero.jpg
tags: [ai, documentation]
draft: false
---

After sharing my article yesterday about [the verification gap in AI content pipelines](https://dacharycarey.com/2026/03/29/ai-content-pipelines-verification-gap/), someone on LinkedIn replied: "I'll take humble human mistakes over confident machine gibberish any day." It's a great line and I appreciate the sentiment, but there's an angle to it that warrants digging into. My first thought was: "I wonder if this commenter knows about the research on how humans are *more likely* to believe the confident-sounding gibberish, and why that's a problem in the context of AI content production pipelines."

Instead of burying the reply in a LinkedIn comment, I decided to write about it here.

## The problem isn't that the gibberish is confident. It's that confidence works.

The commenter's framing assumes we can tell the difference. Humble human mistakes are obvious: typos, awkward phrasing, hedging that signals the writer isn't sure. We're good at spotting those. We've been reading human-written text our whole lives, and we've developed strong intuitions for when a person is winging it.

LLM output doesn't always trigger those intuitions. It's typically fluent, specific, and structurally well-organized. It reads like it was written by someone who knows what they're talking about. And a growing body of research shows that this fluency actively undermines our ability to evaluate whether the content is actually correct.

[Spitale, Biller-Andorno, and Germani (2023)](https://www.science.org/doi/10.1126/sciadv.adh1850) ran a preregistered study with 697 participants and found that GPT-3-generated disinformation tweets were *more convincing* than human-written disinformation tweets. Participants were less able to identify AI-generated false claims as false. Not because the AI was smarter, but because it was smoother. The confident tone and clean prose tripped the same cognitive shortcuts we use to assess credibility in human writing, and the AI hit those marks more consistently than actual humans did.

[Jakesch, Hancock, and Naaman (2023)](https://arxiv.org/abs/2206.07271) found the same pattern across 4,600 participants in six experiments. People couldn't reliably detect AI-generated text, and they relied on flawed heuristics (like associating first-person pronouns with human authorship) that the AI naturally produced. The researchers described AI-generated text as being perceived "more human than human."

This isn't a quirk of one study. [Rathi, Jurafsky, and Zhou (2025)](https://arxiv.org/abs/2507.06306) demonstrated that LLM overconfidence leads directly to user overreliance, and that this effect holds across five languages. It comes down to a simple mechanism: when text sounds certain, readers treat it as certain. LLMs almost always sound certain.

## Fluency is a credibility shortcut, and LLMs exploit it by default

There's a name for this in cognitive science: processing fluency. Information that's easy to process feels more true. The original research demonstrated this with perceptual features like font contrast ([Reber & Schwarz, 1999](https://doi.org/10.1006/ccog.1999.0386)), but [Alter and Oppenheimer (2009)](https://doi.org/10.1177/1088868309341564) argue convincingly that the effect generalizes across all sources of fluency, including linguistic fluency. Clear writing, good grammar, specific details, logical structure: these all make content easier to process, and easier processing registers as a credibility signal. They're decent heuristics for human-written text, where the effort required to write clearly often correlates with expertise.

LLMs break that correlation. Producing fluent, well-structured, specific-sounding text is what they're optimized to do. It costs the model nothing to sound confident about a fabricated detail. The fluency is a structural property of how the model generates text, not a signal that the content has been verified or that the model "knows" what it's talking about. But our brains treat it as a signal anyway, because that's what fluency has meant for every other type of text we've ever read.

This is why the "confident machine gibberish" framing is actually backwards. The danger isn't that AI output is gibberish you might accidentally believe. The danger is that AI output *isn't* gibberish. It's polished, plausible, and specifically wrong in ways that resist casual detection. A human writer who makes a mistake usually leaves traces: inconsistent confidence, hedging language, gaps in specificity. An LLM confabulates a wrong OWASP ranking number or a fabricated API method with the same assured tone it uses for everything else.

## This is the verification gap in practice

This connects directly to what I wrote about recently. In my [AI content pipeline](https://dacharycarey.com/2026/03/26/drafting-editorial-content-with-ai/), every single article across twenty published pieces required factual corrections that the automated verification missed. The errors weren't obvious. They were confident, specific, and technically adjacent to truth: a real CVE presented as a current threat when it had been patched seven months earlier, real OWASP documents cited with wrong ranking numbers, real products described with fabricated implementation details.

The automated verification step confirmed these as "accurate" because the claims *sounded* accurate. The seeds were real. The specific details around them were fabricated. And the fluency of the output meant nothing in the text itself signaled a problem. I only caught the errors by clicking every link and questioning every specific claim myself.

[Salvi et al. (2025)](https://www.nature.com/articles/s41562-025-02194-6), published in *Nature Human Behaviour*, found that GPT-4 was 81% more persuasive than human debaters in a controlled experiment. The AI relied more on logical structure while humans used more emotional appeals; the AI's polished, well-organized argumentation was simply more effective at changing minds. Now imagine that persuasive power applied not to a debate but to a documentation page, a code review, or an article that quietly presents wrong details in a way that reads like authority.

[Gerlich (2025)](https://www.mdpi.com/2075-4698/15/1/6) found a significant negative correlation between frequent AI tool usage and critical thinking, mediated by cognitive offloading. The more people rely on fluent AI output, the less they scrutinize it. That's the feedback loop: the output sounds good, so you trust it; because you trust it, you check less; because you check less, the errors get through.

## What this means

The commenter's instinct is right in one sense: human mistakes *are* more honest. When a person writes something wrong, the wrongness usually has texture you can feel. But "I'll take humble human mistakes over confident machine gibberish" assumes you'll recognize the machine gibberish when you see it. The research says you probably won't, because it doesn't look like gibberish. It looks like competent writing.

That's the problem I keep coming back to. Not that AI output is bad. Not that it's useless. But that it's *good enough to fool you*, and the better it gets at sounding right, the harder it becomes to catch when it's wrong. The verification gap isn't a technical limitation we'll engineer away with better models. It's a human cognitive vulnerability that better models make worse.

If you're using AI-generated content in any capacity (and increasingly, you are, whether you know it or not), the question isn't whether you prefer human mistakes or machine mistakes. It's whether you have a process for catching mistakes that don't look like mistakes. Because those are the ones that ship.

## Addendum: this article is its own case study

I drafted this article with Claude. During the editing process, I caught three categories of error that illustrate exactly the problem this article describes.

**Citation by proxy.** The original draft cited a Harvard Kennedy School Misinformation Review article for the claim that AI hallucinations gain persuasive power through their "fluency, coherence, and authoritative tone." When I checked the source, that article was quoting Zhang et al. (2023), a survey paper. And Zhang et al. didn't actually use those words; the Misinformation Review author had paraphrased and added the specific descriptors. The draft was citing a framework paper's interpretation of a survey paper's general observation: three layers of indirection from any empirical finding, presented as a direct quote.

**Misnamed concept.** The draft originally referred to "the fluency heuristic" as the cognitive science term for judging information as more true when it's easy to process. The fluency heuristic is a real term (Hertwig et al., 2008), but it refers to something different: using speed of recognition from memory to make comparative judgments. The correct term for what the article describes is processing fluency. A real term, confidently applied to the wrong concept.

**Fabricated DOIs.** Claude generated two DOIs that looked correct (right format, plausible journal prefixes) but pointed to completely different papers. The Reber & Schwarz citation used `cogp` (Cognitive Psychology) instead of `ccog` (Consciousness and Cognition). The Alter & Oppenheimer citation used a Trends in Cognitive Sciences DOI for a paper published in Personality and Social Psychology Review. Both DOIs resolved to real papers; they just weren't the right ones.

None of these errors looked like errors. The citation-by-proxy read like solid sourcing. The fluency heuristic sounded like the right term. The DOIs were valid URLs that resolved successfully. Every one of them would have shipped if I hadn't checked. And this is an article *about checking*.
