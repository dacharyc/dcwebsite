---
title: "In Agent Skills land, questions for folks who are distributing official company…"
author: Dachary Carey
date: 2026-04-28 09:22:45 -0400
url: /microblog/2026/04/28/in-agent-skills-land-questions-for-folks-who-are/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7454882799842525185
draft: false
---

In Agent Skills land, questions for folks who are distributing official company skills (yes, I may do too many things 😉):

- Do you have a submission process before people start drafting skills? If so, what does that look like? I have been finding in practice that a lot of skills are poorly scoped or not grounded in concrete user issues, and those skills are less likely to be helpful/can cause degradation in task performance, so I'm curious if anyone is building checks for this into their process.
- How are people planning for maintenance? I'm testing a yaml manifest that lists each skill, the DRI, a review cadence, and last review date, with automation to ping DRIs when it's time to review. But now I'm pondering the idea of review triggers - i.e. product releases, new models, big agent platform changes, etc. and how that should factor into maintenance.
- Do you have any SLAs around skills yet, and/or practices around debugging issues reported for specific agent platforms/product configurations, etc.? Who in your org owns this?

Really curious how other folks are handling this. It shares a lot of similarities with other maintenance processes, but combines them in interesting and slightly weird ways. Would love to hear your thoughts!
