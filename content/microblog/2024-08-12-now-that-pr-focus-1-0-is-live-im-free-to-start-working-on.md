---
title: "Now that PR Focus 1.0 is live, I'm free to start working on new features again!"
author: Dachary Carey
date: 2024-08-12 09:12:51 -0400
url: /microblog/2024/08/12/now-that-pr-focus-1-0-is-live-im-free-to-start-working-on/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:ugcPost:7228750283093282816
images:
  - src: /images/microblog/2024-08-12-now-that-pr-focus-1-0-is-live-im-free-to-start-working-on-1.jpeg
    alt: "Screenshot of the PR Focus macOS app's Tagged PRs dashboard showing eight tagged pull requests across repos, with a tag filter and a start and end date filter at the top. Two PRs from the prfocus and prfocus-website repos carry a v1.1 tag (\"1.1 - Tags and Filters\" and \"Add docs and announcement for v1.1: Tags and Filters\"), and four PRs from an SDK Docs repo carry a Consolidation tag, each card showing conflicts, status, checks, commits, comments, and reviews."
  - src: /images/microblog/2024-08-12-now-that-pr-focus-1-0-is-live-im-free-to-start-working-on-2.jpeg
    alt: "The same Tagged PRs dashboard filtered to the v1.1 tag, showing only the two PR Focus pull requests: \"1.1 - Tags and Filters\" (#26, 11 commits, checks passing) in the prfocus repo and \"Add docs and announcement for v1.1: Tags and Filters\" (#54) in the prfocus-website repo. Each card has Add tagging and Add filtering controls."
  - src: /images/microblog/2024-08-12-now-that-pr-focus-1-0-is-live-im-free-to-start-working-on-3.jpeg
    alt: "Screenshot of PR Focus's Create a Filter dialog. The filter is named \"Ignore Snyk PRs\" and applies to PRs where the PR author username exactly matches admin-token-bot; a dropdown shows the other matchable fields, title, description, and repository name. The action is set to Ignore, with an Add more filter criteria button and a Create Filter button."
draft: false
---

Now that PR Focus 1.0 is live, I'm free to start working on new features again!

So here's a sneak peek at what's coming in the next release...

- The ability to tag PRs in PR Focus.
   - Easily track work related to a specific project or release, even across repos.
   - View PRs with a given tag, and optionally within a specific date range.
- The ability to create filters in PR Focus.
   - Automatically Watch, Ignore, or Tag incoming PRs that meet criteria you define.
   - Have Bug/Feature naming conventions? Tag all the PRs that come in with your title criteria with a "Bug" or "Feature" tag so you can easily keep track of important work.
   - Want to always ignore a certain type of PR? Automatically Ignore PRs you don't care about, such as dependency update PRs from Snyk.

These new features are all *in*, working, and tested - I'm just cleaning up UI elements preparing docs! Expect to see these in an update within the next week or two. 🎉
