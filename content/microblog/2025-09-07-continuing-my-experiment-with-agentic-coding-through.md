---
title: "Continuing my experiment with agentic coding through Copilot, I noticed the…"
author: Dachary Carey
date: 2025-09-07 16:22:27 -0400
url: /microblog/2025/09/07/continuing-my-experiment-with-agentic-coding-through/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7370552049786097664
draft: false
---

Continuing my experiment with agentic coding through Copilot, I noticed the other day that some tasks where Claude had been doing just fine suddenly seemed to be beyond it. And some tell-tale behaviors and quirks started to creep into the chat.

I went digging and found a discussion on the GitHub message boards that GH will switch the model you’re using, even if you have explicitly selected a specific one, to a different model based on - they won’t quite say. Something something “availability.”

Without telling you.

It’s a quiet switch-a-roo under the covers.

Went digging for more info, and found all the stuff about “premium” model usage in the GitHub docs that wasn’t on my company wiki about using our enterprise Copilot access. It didn’t even occur to me that I basically have a quota limiting my “premium” model access which I can use up on tasks that don’t necessarily need it, and then they’ll quietly switch me to a less competent model without saying a word.

Did some exploring about switching my weekend project to pay Anthropic directly for Claude access in my personal projects, and - wow, it’s not cheap, and the quota is LOW and also very vague. From Anthropic’s docs, their mid-tier plan provides “45 uses per 5 hours for most low-intensity, low-compute tasks” - which most of my agentic stuff is not.

Went looking at JetBrains stuff and the quota is even lower and more vague.

So now I’m using Ollama with a few different local models through the JetBrains “AI Assistant” integration, and have basically dropped agentic workflows. I’m back to writing code by hand, and using AI as an occasional syntax reference instead of dropping out of my IDE to a browser to look something up.

While I was at it, I rewrote a bunch of stuff that the AI wrote last weekend to be much cleaner, simpler, and use better patterns. Apparently AI doesn’t know how to make code simple and DRY.

This may be a short-lived agentic coding experiment. I’ll try a bit more in a work project I’ve got going, but I might be through with agents for now in my personal projects.
