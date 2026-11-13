---
title: "This week, what I'm thinking about in agent experience land is learning design"
author: Dachary Carey
date: 2026-11-13 10:24:18 -0500
url: /microblog/2026/11/13/this-week-what-im-thinking-about-in-agent-experience-land-is-learning-design/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7527028576311783425
draft: false
---

This week, what I'm thinking about in agent experience land is learning design. What should a workshop series about eval design teach people?

Before I started at NVIDIA, I was convinced that eval engineering was its own new-and-emerging discipline, not a skill set that existing engineers or product people have. Now that I've been here for a few months, I am seeing that I am right that eval engineering isn't something that engineers can "just do" even if they're very good engineers. But with a company at this scale, there also can't be a centralized group of eval engineers who run evals for the whole org.

Eval engineers need eval design and authoring expertise, but they also need to partner closely with SMEs who know the products well and can apply judgment about the product-related facets of eval design. What would a person using this product know already? What software would they already be running? What task are they trying to do, and how do we know if the agent is "successful" at that task? Those are product-specific questions.

So now I'm thinking to scale across hundreds of products, we can't consolidate eval knowledge in a core group of eval engineers - we need to teach every product team how to create and run their own evals alongside their regular test suites. Every team needs to be able to curate its own data sets that represent their users, the products, and the tasks they need to perform, and should be able to design scoring logic and run the evals on a schedule that makes sense for their product needs.

But those tasks require new skills that product teams don't already have. So how do we help them learn what they need to know?

I'm running a workshop series with a pilot cohort to teach eval design concepts. But deciding what the most important details are that I need to somehow squeeze into 8 sessions is - a challenge. I have an engineer who wants to go into the weeds and look at code. I have three people who have been doing eval authoring/design already, and I'm trying to deepen their knowledge of the principles that they are seeing show up in practice. I've got one person who has a deeper interest in/knowledge of evals and who could essentially be a TA for the workshop. And I've got five folks who have mostly not been running evals, except as the occasional one-off. These groups could really all use different courses, but I am one person whose job isn't necessarily full-time learning design, so I'm trying to figure out what the more general course looks like while parking specific questions for possible future deep-dives.

All this to say - hey folks, running evals is a new discipline that requires its own skill set. (Or really a handful of related new disciplines - data set design, eval environment design, eval running and infrastructure...) I predict this is going to emerge as a new job cluster as companies get more serious about improving how effectively AI can use their products.
