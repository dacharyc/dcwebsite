---
title: "Continuing my ai-assisted coding spelunking, I've come up against \"premium…"
author: Dachary Carey
date: 2025-09-12 11:11:28 -0400
url: /microblog/2025/09/12/continuing-my-ai-assisted-coding-spelunking-ive-come-up/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7372285731597197312
draft: false
---

Continuing my ai-assisted coding spelunking, I've come up against "premium request" limitations with my enterprise-sponsored Copilot access. So now I'm branching out to explore other model options and IDE integrations.

JetBrains provides their own AI assistant, Junie, with agentic capabilities. I really like the Junie UX, but I can't get it to actually - work. It keeps failing with execution errors without successfully completing tasks. I put Claude on the most recent task after Junie failed, and Claude also completed the task substantially faster than Junie was working before it failed.

I really want to like Junie, but in my limited testing, it seems not quite ready for real-world usage. (Also, the quotas are ridiculously low. FAR lower than Copilot.)

JetBrains *does* let you BYO model provider using their AI Assistant integration. I've now started experimenting with local Ollama models running on my machine, which doesn't use up any quota. The models are so far a bit less capable. I iterated with Ollama's gpt-oss for a while and circled around a bug, and then switched to Copilot with GPT 4.1 and got the bug fixed right away. So I think Ollama may be useful for general question/answer stuff on high-level syntax things, but for help debugging, I haven't found the right model yet.

I also wrote a little macOS menu bar app to help me keep tabs on my premium model usage, since apparently this is an issue. I have 31 Claude requests remaining for the month - wish me luck!
