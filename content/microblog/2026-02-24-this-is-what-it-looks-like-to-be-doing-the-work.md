---
title: "This is what it looks like to be doing the work"
author: Dachary Carey
date: 2026-02-24 22:41:37 -0500
url: /microblog/2026/02/24/this-is-what-it-looks-like-to-be-doing-the-work/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7432268509142827009
draft: false
---

This is what it looks like to be doing the work. Mintlify's own docs set - a docs platform whose premise is "built for both humans and AI" - passes all the Agent-Friendly Docs checks I've implemented so far, save one.

Mintlify's docs return "soft" 404s (returning a 200 with "no content" for a page that doesn't exist instead of a clearly actionable 404). But this is pretty minor, easily fixed, and would only affect a small subset of content in practice.

The fact that they're already doing all the things I identified as important for agent friendly docs means they - and their customers - are ahead of the curve. This is how you spot the real deal, folks.

(Also, now I have a "known good" docs set to test my tooling against as I continue to add checks 😁)
