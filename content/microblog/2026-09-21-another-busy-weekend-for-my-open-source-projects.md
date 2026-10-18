---
title: "Another busy weekend for my open-source projects!"
author: Dachary Carey
date: 2026-09-21 08:14:42 -0400
url: /microblog/2026/09/21/another-busy-weekend-for-my-open-source-projects/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7507774303522873344
draft: false
---

Another busy weekend for my open-source projects! Lots of releases:

- skill-validator: the term "characters" in the Agent Skills spec is ambiguous. I chose UTF-8, but someone from China pointed out runes would be more correct, as UTF-8 was cutting off the description early. So I did a little poll to see how the different harnesses implemented description length, and it turns out there are at least 4 different choices of character type already implemented in the ecosystem, and probably more I couldn't see 😬
- agent-skill-implementation: I added a couple of new fixtures and checks so we can keep an eye on what type of "characters" the different harnesses support.
- agentsummons: updated for current harness versions
- agentminutes: updated for current harness versions, added a new record type I observed for the first time in Claude Code related to session cost
- skillxp: updated for current harness versions, agentsummons and agentminutes, added drift probing similar to the other libs/CLIs
- agent-traffic-classifier: revisited the current state of agent traffic, including adding a lot of new harnesses, updating some of my heuristics for existing harnesses that no longer behave in the same way, and saw two new large-scale crawlers that don't want to identify themselves...

I also wrote a couple of articles (that still need to get approved through a publishing process at work).

Lots going on in agent land when I skip my weekend chores 😅
