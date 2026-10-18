---
title: "Interesting research paper dropped on the 20th about how coding agents…"
author: Dachary Carey
date: 2026-08-23 12:11:05 -0400
url: /microblog/2026/08/23/interesting-research-paper-dropped-on-the-20th-about-how/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7497324547910549504
draft: false
---

Interesting research paper dropped on the 20th about how coding agents discover, read, and write technical documentation: https://arxiv.org/pdf/2608.20195

The main finding is that agent instruction and plan files are the primary agent documentation interaction interface, with agents rarely consulting classical genres that documentation teams focus on (10.6%) and far more rarely consulting API documentation (1.3%)

BUT there are some problems with the methodology and data set. They've scoped specifically to "repository-local, file-based documentation interactions." It doesn't observe docs read through a browser, and explicit documentation-retrieval tool calls were almost entirely missing from their data set (only 16 events).

Also, the data source skews heavily toward one usage pattern. It's 83.8% Claude Code sessions, and Codex has pretty different reading characteristics. It is also entirely composed of developers who opted into one service's checkpoint logging feature on public repositories, with short routine sessions over-represented and most failed sessions never captured. This dataset self-selects into a specific usage pattern or behavior that isn't representative. They also encountered firsthand the issues that prompted me to write `agentminutes` to normalize session logs from different harnesses into comparable event types.

People who only read the opening numbers may conclude "agents don't use API docs" (based on the 1.3% number) but really, what they've shown is that "when working inside a repo, agents rarely read local API reference files."

There's a lot of missing context in the methodology and data set of most research papers, and it's important to dig in so you can understand what the findings really show.
