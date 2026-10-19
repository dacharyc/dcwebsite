---
title: "I've had the opportunity to speak to several different folks"
author: Dachary Carey
date: 2026-10-19 08:38:07 -0400
url: /microblog/2026/10/19/ive-had-the-opportunity-to-speak-to-several-different-folks/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7517927061244665856
draft: false
---

I've had the opportunity to speak to several different folks across the industry about quality in docs in the last few weeks, and one thing keeps coming up in all the conversations: contradictions in first-party surfaces. A lot of us are wrestling with this in some form or another. 

For some people, it looks like terminology challenges. Using the same term to refer to different things within or across products, or using different terms to refer to the same thing.

For other people, it looks like different claims about product capability. One document says the product can do FOO, another document omits or contradicts that claim, and suddenly you're publishing two different facts about product capability. Anecdotally, I've seen this one a lot in the past in the differences between marketing content and product docs, or point-in-time blog content not being kept up-to-date with product changes.

These types of issues don't just affect public-facing documentation and erode agent experience for your customers. They also plague internal systems and slow down your own company's product and engineering teams.

Think about your internal documents. A product team writes a proposal for a new feature in a Google Drive document. It gets implemented. Engineers document it in the code, or maybe in-repo documentation. You've got a wiki somewhere with details about the roadmap and supported features. You've got a ticketing system somewhere to track work. All of these systems expose connections for your agents, and many may now contradict each other because drift happens.

Which source of truth is canonical? And how do we communicate that to agents? And what happens if your product is versioned - how do we expose that to agents, and how are they supposed to reason about changes between versions?

If your head is exploding right now, it should be. This is one of the hardest problems around. It has been a human problem for ages, but moving at the speed of agents, these contradictions now compound *so fast*.

The solutions probably look different for different teams and data sources and objectives. I think we're going to be wrestling with this one for a long time to come.
