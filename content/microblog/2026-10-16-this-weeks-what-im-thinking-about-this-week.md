---
title: "This week's 'what I'm thinking about this week'"
author: Dachary Carey
date: 2026-10-16 18:32:40 -0400
url: /microblog/2026/10/16/this-weeks-what-im-thinking-about-this-week/
topics: [ai, agents, coding, personal]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7516989520467329024
images:
  - src: /images/microblog/2026-10-16-this-weeks-what-im-thinking-about-this-week.jpeg
    alt: "Screenshot of one topic from a daily agent-experience briefing. The heading asks \"Verifier validity and brittleness: are agent eval score changes capability or measurement artifact?\" A summary paragraph says the thread opens with two papers from the same day that attack validity from different sides: \"Shortcut Sensitivity in Agent-Safety Evaluations\" finds that safety-guard benchmarks carry spurious cues such as shell command length, tool markers, and domain overlap that inflate guard scores, and \"Stronger Judges Do Not See More: Deployment-Integrity Blindness in LLM Evaluation\" shows output-only judges cannot detect tampered runtime stacks that preserve outputs and proposes manifest-hash identity checks. A \"For you\" recommendation says judge strength is not a fix for measurement validity and any eval claim should record the stack identity and be audited for shortcut cues. Both papers are listed as arXiv links tagged ax and measures, and a Signals section notes the status moved from emerging to active, with 2 new of 5 observations and 4 in the last 7 days."
draft: false
---

This week's "what I'm thinking about this week" isn't about work, since I had the week off work! (Well, at least not directly.)

I did get some downtime in, but if you know me, you also know that when I'm not working I'm working on personal projects. So much to do, so little time. So this week I've been focused on a new pipeline to surface agent experience-related topics from across the industry.

This uses my new `topicsleuth` library to analyze content I feed it from arXiv, product releases, industry bloggers and reporters, and other sources I care about. It uses domain-specific rubrics to identify agent experience-related content and assess how the content relates to AX topics I'm tracking, or propose new ones.

Then, create a daily briefing that surfaces relevant content for me from high-value topics. And a weekly briefing that helps me track changes over time, and make some forward-looking projections about where the industry is going. Because this is a single-user project, I can be very specific - the screenshot below is one of the topics from today's run, with recommendations that the model made for me based on what it thinks I care about. (It's pretty good!)

It can help me track changes by vendor, can help me spot consolidation or bifurcation in the industry, and spot emerging trends or identify topics that are fading in importance. And because I've been tracking related content since I started my research pipeline in March, I was able to backfill this new project with thousands of pieces of content so I can already see changes over time.

🤞 this helps me keep an eye on emerging and maturing trends in agent experience as this new field is being defined. (And yes, it's wild and amusing to see my own content feeding these topics, too!)
