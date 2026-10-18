---
title: "At the end of Week 2 of New Job, here's what I'm thinking about: evals"
author: Dachary Carey
date: 2026-07-31 09:00:33 -0400
url: /microblog/2026/07/31/at-the-end-of-week-2-of-new-job-heres-what-im-thinking/
topics: [ai, agents, business]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7488941672676896768
draft: false
---

At the end of Week 2 of New Job, here's what I'm thinking about: evals.

A lot of people lean heavily on evals as a tool to assess agent task completion performance. But we don't have a shared idea of what "good" looks like. People are indexing on different assessment criteria, tracking different metrics, relying on different evaluation conditions, or working with custom evaluation frameworks that may not follow design best practices. So Team A's evals may be very different than Team B's evals, and as a person trying to read eval results and take some action, those differences matter - and many of them may be invisible to the person reading a report.

Separately, in terms of thinking of evals as an agentic analog to unit tests, I'm thinking about how we can be sure the evals are running under a representative range of conditions. Just as in a unit test situation, we can't only run a given evaluation under the happy path conditions. We need to be thinking about the range of conditions under which our customers may be using our products; brownfield projects, greenfield projects, with properly-configured tools, with mis-configured tools, with only a subset of tools - and understand how agents perform in each of these conditions. A single task with a single environment configuration and no conditions isn't a representative picture of how agents perform.

And finally, I'm thinking about how we connect evals to the other facets of the DX process. If you do work with user journeys, jobs-to-be-done, personas, and other exercises mapping DX surfaces - how do we connect evals to those mappings to make sure the DX surface is well covered? Agent experience needs to work within agent capabilities and harness constraints, but it isn't completely independent from the rest of your DX life cycle, and those connections should be considered in eval design.
