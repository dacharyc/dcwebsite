---
title: "Thanks to @philip"
author: Dachary Carey
date: 2026-04-17 09:22:28 -0400
url: /microblog/2026/04/17/thanks-to-philip/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7450896465998159872
draft: false
---

Thanks to [@philip](https://github.com/philip). - AFDocs now supports testing against preview/staging deployments with different origins 🎉

In v0.13.0, we've added a new `--canonical-origin` flag that lets you specify a production origin to point to for URL matching in llms.txt and/or sitemap when you're running against a staging build. This looks like:

`afdocs check https://preview-xyz.vercel.app/docs --canonical-origin https://example.com/docs`

For more details, check out the updated "Run Locally" docs at: https://afdocs.dev/run-locally.html#production-urls-in-local-builds

I really appreciate the PR, and hope this is useful for folks who want to test from staging.
