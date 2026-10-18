---
title: "A thing I have been seeing is that it’s really hard for product teams to come…"
author: Dachary Carey
date: 2026-09-30 08:33:01 -0400
url: /microblog/2026/09/30/a-thing-i-have-been-seeing-is-that-its-really-hard-for/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7511040405828800512
draft: false
---

A thing I have been seeing is that it’s really hard for product teams to come up with realistic user prompts for running evals.

I have been leaning on traditional developer experience artifacts to help with that. If you’ve got personas, jobs-to-be-done, and user journeys for your product, you can pick a persona, a job, and potentially a journey stage to create realistic prompts for that user. So developer experience is still the right starting point for agent experience when it comes to writing eval prompts.

Another thing you can do is use public GitHub projects to derive eval prompts. If you’re tracking public projects where your product is used, you can look at PRs that have integrated or configured your product, and those are *real* user jobs to be done that you can derive prompts from. Public GitHub data carries a cheating risk, though, in that agents with Internet access can go look at the actual completed work on GitHub and provide an answer rather than reasoning about it.

If you run evals to understand how agents use your products, how do you derive prompts? Does anyone have any other good tips or techniques to share?
