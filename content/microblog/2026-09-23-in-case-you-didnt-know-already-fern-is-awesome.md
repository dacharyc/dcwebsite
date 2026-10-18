---
title: "In case you didn’t know already, Fern is awesome"
author: Dachary Carey
date: 2026-09-23 13:18:07 -0400
url: /microblog/2026/09/23/in-case-you-didnt-know-already-fern-is-awesome/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7508575436000604161
draft: false
---

In case you didn’t know already, Fern is awesome. They just added something new to make it easier to test their Ask AI functionality because I asked them to 💖 👏

For some upcoming work, I wanted to run a small eval project to see what information users would get in response to questions using the Ask AI widget in our Fern-hosted docs. They expose a Post Chat Completion endpoint that gives you programmatic access to the tool, but I noticed two things:

- The widget scopes retrieval to pages under a product path, but the endpoint didn’t expose the ability to scope the retrieval similarly
- When I was hitting the endpoint, my queries were showing up alongside user data in the tool analytics, potentially skewing the “real” user queries. There was no way to say “I’m just testing, don’t count this in analytics”

I raised both to [@Ryan-Amirthan](https://github.com/Ryan-Amirthan) and now Fern has shipped 2 new params to help!

Big thank you to all involved.

Related, if you’ve got docs on Fern, did you know you can programmatically test and run evals to see if your users are getting the answers you expect? It’s a great way to learn about gaps or conflicting info in your docs. (It does get billed toward your usage, though, so use it strategically.)
