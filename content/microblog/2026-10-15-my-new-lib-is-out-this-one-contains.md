---
title: "My new lib is out! This one contains the logic that runs"
author: Dachary Carey
date: 2026-10-15 15:17:04 -0400
url: /microblog/2026/10/15/my-new-lib-is-out-this-one-contains-the-logic-that-runs/
topics: [ai, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7516577907528454145
draft: false
---

My new lib is out! This one contains the logic that runs my research pipeline. To use it, you find content you're interested in and feed each piece of content through the lib. The lib:

- Uses vector embeddings to pull semantically-related topics from a vector store
- Passes the similar topics and the piece of content to an LLM and asks it to say whether the content is related to any similar topics, or warrants a new topic
- For each connection, it asks whether the content supports a topic, extends a topic, contradicts a topic, or marks a shift in the topic (i.e. something that was previously only research is now a feature shipped in a product)
- You can optionally use a second model - either an LLM or a decision model like Jev - to provide a second opinion if a new topic is being proposed
- It also tracks topic lifecycles, noting whether a topic is emerging, active, mature, fading, or dormant

It comes with the machinery to tune results based on how well the LLM is identifying content relative to your topics. I have found that it worked better with an earlier version of Opus, but a newer version was more likely to propose new topics (new topic creation went from 20% to ~40+%). So best results may require making some tweaks until the topic creation and assignment matches your human judgment, and you may need to tune things as parts of your pipeline change.

This has been working well for me in a pipeline for ~7 months, but I've broken it out into a lib because I want to use it in a couple of new ways/projects. If you have any need to watch how topics emerge, grow, and change over time, you may find it useful - look for `topicsleuth` on PyPI or I'll throw a link to the GitHub repo in the comments.
