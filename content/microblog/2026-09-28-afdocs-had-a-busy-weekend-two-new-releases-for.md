---
title: "AFDocs had a busy weekend; two new releases for documentation teams who want to…"
author: Dachary Carey
date: 2026-09-28 08:44:02 -0400
url: /microblog/2026/09/28/afdocs-had-a-busy-weekend-two-new-releases-for/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7510318404075970560
draft: false
---

AFDocs had a busy weekend; two new releases for documentation teams who want to check their docs for agent-friendliness:

- v0.21.0 is a bug fix and non-breaking enhancement release; thank you to [@JakeSCahill](https://github.com/JakeSCahill) for reporting issues and [@danielcalvolopez](https://github.com/danielcalvolopez) for a PR to emit progress notifications
- v0.22.x is a breaking release that introduces 5 new checks, bringing it up to spec v0.6.0, which changes scoring and may require sites to do new work to improve agent-friendliness

Notably, one of the new checks calls for markdown versions of pages to contain absolute links instead of relative. When migrating to v0.22.0, I had to update several of my sites that only contained relative links. This makes the markdown more portable, and solves several cases where agents could fail to construct the correct URL if it needs to load a related page.

Because v0.22.0 is a breaking change, I've added a new "Upgrading to v0.22.0" page to the docs.
