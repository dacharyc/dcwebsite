---
title: "If you are a technical writer who documents software, learning to use your…"
author: Dachary Carey
date: 2025-08-01 07:55:52 -0400
url: /microblog/2025/08/01/if-you-are-a-technical-writer-who-documents-software/
topics: [documentation, coding, business]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7357016214532964353
draft: false
---

If you are a technical writer who documents software, learning to use your version control system's APIs is a force multiplier.

It's easy for Jira or other tracking processes to fall behind reality. When you document software, the code is the source of truth. So don't watch Jira - watch the code!

I learned GitHub's APIs to write PR Focus, an app that let me keep track of engineering feature PRs. Since I work in a docs-as-code workflow, it also let me easily stay on top of my work.

Since then, I've been writing little utilities that use GitHub's APIs.

I've got a script that gets traffic metrics from some public-facing code example repos we maintain. I write the metrics to a database which populates a dashboard for stakeholders. This lets us make data-driven decisions about developer engagement with code examples and how much we want to invest in maintenance.

This week, I added a script to check the release versions of the products we document and compare them against the versions we use in our test infrastructure. It automatically creates tickets for my team to update test infrastructure when we fall behind a release version.

Learn the APIs that can give you information about the state of code that matters to you. Code is the source of truth in software documentation - not Jira.
