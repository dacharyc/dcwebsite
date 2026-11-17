---
title: "AI-assisted development thoughts related to weekend personal project"
author: Dachary Carey
date: 2026-11-16 08:59:06 -0500
url: /microblog/2026/11/16/ai-assisted-development-thoughts-related-to-weekend-personal-project/
topics: [ai, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7528094299377348608
draft: false
---

AI-assisted development thoughts related to weekend personal project:

- I'm using Fable 5.2 as a thinking partner, Opus 5.5 for more complex implementation, and Sonnet 5.5 for simpler/grunt work implementation. Fable helped me break down the tasks according to which ones are best suited for each model. I have a lot to accomplish this week and want to make the most of my subscription.
- I'm using Astra to review Fable's work. Astra has caught a lot of edge cases, data race conditions, and conflict-related cases that Fable didn't catch. I have historically been pretty happy with Claude but using Astra to review Claude's work has made me question everything 😂 😭
- Fable has told me at least a dozen times - I've stopped counting - that the exploration work we've been doing around the data model is "enough" and we should just build. But every new round we find something we need to think through and codify rules around before writing the system, and I've lost track of the number of data model changes we've made already. It's a bit of a bummer that the Anthropic model that is optimized for reasoning and heavy-duty work keeps trying to stop working.
- What I'm doing now is *very* far from vibe-coding. I've got hundreds of tests so far, a lot of individual UI views, and several validation-related tools. I'm really glad to have done so much app development work as a human writing my own code before AI came along, so I can steer the process intelligently and actually weigh in on decisions that will impact app stability and user satisfaction.
- A non-zero amount of my stubbornness around having a solid data model before I build a persistence layer is based on how annoying I find it to deal with Apple's CloudKit containers, and migration. If everything about that wasn't so annoying, I would be more relaxed in my development approach. I'm going through a lot of extra work to avoid what is arguably product problems/bad UX for Apple's persistence stuff.

I was hoping this would be a quickie app to put together, and it is proving to be Not That. 😮‍💨

OTOH I have been working with Opus on marketing stuff in the background while implementation work is happening, and that has been surprisingly good. 🤷🏻‍♀️
