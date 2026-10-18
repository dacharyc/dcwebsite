---
title: "Week 6 at New Job, and here’s what I’m thinking about in agent experience land"
author: Dachary Carey
date: 2026-08-28 08:46:25 -0400
url: /microblog/2026/08/28/week-6-at-new-job-and-heres-what-im-thinking-about-in-agent/
topics: [ai, agents, documentation, business]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7499084975476985856
draft: false
---

Week 6 at New Job, and here’s what I’m thinking about in agent experience land:

Enabling agents to discover the right information at the right time is still a huge unsolved problem. Skills are an attempt to do that, but they have their own problems, from harness/model combinations just NOT choosing to invoke them to authors writing them at the wrong level of abstraction or filling them with unneeded/unhelpful information.

Progressive disclosure is the principle at play, with the recent “The New MCP Roadmap” announcing a progressive discovery effort for MCP tools, a problem I’ve seen firsthand with enterprise customers. Skills also attempt to provide progressive disclosure. It’s the right principle IF you can get agents to decide they need more information and actually request it.

A problem I’ve continued to see across all “agents must request more information to successfully complete a task” - whether that be load a tool, visit a docs page, or use a skill - is that plenty of times, the agent just chooses not to request more information. It proceeds with what it has, guesses, and fails, and has to spend time/tokens recovering or just fails to recover.

I’ve also been thinking about Opus, specifically; I *think* some of its “more expensive, slower, over-engineering” tendencies may be related to trying to solve this. I.e. I think Opus is trying to get the information it needs to complete the task successfully, but it’s manifesting as these behaviors.

So, hard problem remains hard.
