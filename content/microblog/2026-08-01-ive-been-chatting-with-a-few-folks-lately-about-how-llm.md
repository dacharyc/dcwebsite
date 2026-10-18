---
title: "I’ve been chatting with a few folks lately about how LLM writing is just… not very good"
author: Dachary Carey
date: 2026-08-01 23:29:59 -0400
url: /microblog/2026/08/01/ive-been-chatting-with-a-few-folks-lately-about-how-llm/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7489522863750909952
draft: false
---

I’ve been chatting with a few folks lately about how LLM writing is just… not very good. They use a lot of words to say very little. And we’ve all gotten to the point where certain words trigger us because we see them in AI slop *everywhere.*

But. I also use AI to help speed up my writing process sometimes. I scaffold content with a quick sketch of my ideas and sources to pull from, and let AI help me fill in details.

And then I edit judiciously because of those speech patterns, trigger words, and verbosity.

I finally decided to do something about it and hook up Vale in some of my personal projects, with some rules designed to target AI writing. I’ve pulled some from third party rule sets that folks have published, and made some of my own. I’ve set it up with a pre-commit hook so no new or edited file with rule violations gets committed.

Style guides in markdown, like in skills or rules in AGENTS.md, are just suggestions agents can ignore. Linters that block commits force agents to follow the rules. If you use AI for writing, lint your prose.

https://vale.sh/
