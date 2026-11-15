---
title: "Oh dang... had a new app idea and now a new domain is registered"
author: Dachary Carey
date: 2026-11-15 11:55:47 -0500
url: /microblog/2026/11/15/oh-dang-had-a-new-app-idea-and-now-a-new-domain-is-registered/
topics: [coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7527776377111072769
draft: false
---

Oh dang... had a new app idea and now a new domain is registered and I'm deep in app design land. I definitely needed another project 😅

I'm starting with one of my favorite parts: data modeling. Data modeling has been one of my favorite development exercises since long before I ever started writing code. I would nerd out with my wife and help her brainstorm data models for *her* web apps back in the day.

I love thinking about the abstractions that are relevant for any given app. The intersection of what the data structure needs to carry to model the domain, plus what the user needs to do with the data to inform what details are relevant to model, is one of my favorite types of thought exercises.

I start by looking at the domain I need to model, and thinking through what details are relevant to represent the aspects of the domain my app needs to care about. After I've done the initial brainstorm, I map examples I want to use to my model and see if it holds up with real use cases. I try to pick a range of representative examples to see if any example teaches me something different about what the data model needs.

Then I think about UX representations and user actions. What does the *user* need to know about the domain? What actions might the user want to take? Which things need to carry state across views? (And yes, now the user might be a human or an agent, and they might want to take different actions!) I give my apps a "screenshot mode" to make it easier to generate App Store screenshots, and use in-memory Swift structs to test the data model with app views and data flow to think through UX interactions.

Only once it survives the real-world examples and the UX representations and actions do I consider the model settled enough to start storing data. Mainly because I hate having a bunch of migrations before I ever ship v1 😝
