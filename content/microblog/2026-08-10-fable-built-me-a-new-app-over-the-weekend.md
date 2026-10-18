---
title: "Fable built me a new app over the weekend"
author: Dachary Carey
date: 2026-08-10 08:33:25 -0400
url: /microblog/2026/08/10/fable-built-me-a-new-app-over-the-weekend/
topics: [ai, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7492558726357278720
images:
  - src: /images/microblog/2026-08-10-fable-built-me-a-new-app-over-the-weekend.jpeg
    alt: "Screenshot of an iPad app on its Today tab. A sidebar lists Today, Archive, Saved, Themes, and Metrics. The main pane shows a reading list of ranked items with eval scores: \"Quoted $1M for AI code review. Built it for free\" (0.68), \"GitHub Models is now retired\" (0.66), \"Claude.md, except it's meaningful and it works\" (0.60, checked as read), and \"Show HN: NexusMem – Local context memory engine for AI coding agents\" from Hacker News, linked to the theme Context State Management and Virtualization. Below that, a Pipeline Health section shows Commentary (8 evaluated, 2 above threshold, 1 selected, 2 drafts), Research (1 source, 1 observation, 326 themes tracked), a Narrative entry, and API balances for Anthropic and OpenAI with the amount spent this month."
draft: false
---

Fable built me a new app over the weekend. It took a few hours of iteration, mostly because I didn’t know exactly what I wanted well enough to specify it up front.

Aside from some minor CloudKit Sync issues, and some stuff you have to do in Apple Developer UIs, I did nothing except chat and everything *just worked* on the first try. I did read the code for quality/security stuff and it’s very reasonable.

This is a jump from the last app I wrote with AI. These things continue to get better. Unlike the last time, Fable proactively launched the simulator and inspected the UI through screenshots. It built scripts to generate sample data for UI iteration from real data I had on disk. It read existing CI pipelines to figure out how the data was generated and found a minimal, no-fuss way to fit a step into an existing pipeline to generate a version of the data that this app could use. Even just 6 months ago, I would have had to do so much of this myself.

I’ve spent this year genuinely believing this is the direction that software development is moving, but it’s wild to see it in practice.

(The app is something I won’t publish beyond private TestFlight; it compiles a reading list for me and gives me a native view of some data from a daily report I generate for myself from a series of CI pipelines that run overnight. A new CI step emits as JSON the same data that powers a web dashboard for me. Now I can read my morning briefing from my iPhone or iPad, and read state syncs between both.)
