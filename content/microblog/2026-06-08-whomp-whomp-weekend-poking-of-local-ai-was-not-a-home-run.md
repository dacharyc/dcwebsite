---
title: "Whomp whomp, weekend poking of local AI was not a home run"
author: Dachary Carey
date: 2026-06-08 08:36:52 -0400
url: /microblog/2026/06/08/whomp-whomp-weekend-poking-of-local-ai-was-not-a-home-run/
topics: [ai, agents, documentation, personal]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7469729160677068800
draft: false
---

Whomp whomp, weekend poking of local AI was *not* a home run. I'm not quite ready to give up, but I'm a lot less optimistic than I was about getting something useful out of the process. I'll write up the details when I get a chance, but some takeaways...

The good:
- Pi is an *awesome* harness if you want to customize. The analogy to a bare-bones IDE that you can built out to whatever your heart desires absolutely holds here; it's like an agent VIM or Emacs that you can turn into whatever you want.
- I have apparently used enough harnesses at this point that I have Opinions about what I like/dislike/what behaviors I want. So being able to write my own tool implementations or extensions to guide behavior is super cool and could become very addictive.
- Running right-sized models on my Windows GPU is BLAZING FAST. I have never seen an agent work this fast; it streams and makes tool calls much faster than I can read. It really makes me want to dig into why it's so fast compared to the big-provider APIs I'm usually working with; presumably it's the lack of reasoning, but I want to learn more.

The bad:
- Pi has basically no batteries included. So you *have* to customize just to get to basic functionality, like a permissions system or web retrieval functionality. It'll probably make sense to have starter distros that come with what it takes to get a functional agent.
- Getting local models to run is... a wild time-consuming rabbit hole. I spent 7 hours in lib conflict/bug resolution hell to get Qwen3-Coder running locally; 3.5 hours just getting things to compile, and longer to get it running past the profiling stage and serving on my local network. And then I installed a completely different stack to use Devstral. It is not for the faint of heart and would have taken me SO LONG (days) without Claude Code.
- Even with every optimization I could throw at it, the local models are just not up to even basic tasks. Qwen3-Coder offers better code gen, at the cost of being unreliable at driving the harness. Its tool call format doesn't play nicely, and it's not as good at instruction following and tool calling in general as other models. Devstral drove the harness like a pro, using *all* the tools quickly and consistently, but then crapped out when I tried to get it to generate docs updates. It was a basic generation task and it just kept stalling. I'll try again but am not terribly optimistic.

My big takeaway is that while the local stack is fun to play with, and I love the *idea* of Pi, I'm absolutely spoiled in my expectations of what agents should be capable of based on using frontier models all the time through paid subscriptions or at work. The local agents I can run on my hardware are where the frontier was a year or two ago. I'm on a beefy gaming system; if I wanted to step up to something that could run more capable models, I'd be looking at $25k to build a completely different system. Not quite where I was hoping to be at the end of the weekend.
