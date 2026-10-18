---
title: "One of MongoDB's core values is #buildtogether "
author: Dachary Carey
date: 2023-01-23 09:49:15 -0500
url: /microblog/2023/01/23/one-of-mongodbs-core-values-is-buildtogether/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7023300622716149760
draft: false
---

One of MongoDB's core values is #buildtogether . This is one of my favorites, because the end result is always something better than an individual could achieve alone.

As an example, I led a Docs Maintenance Working Group a while ago that attempted to systematically identify maintenance needs so we can proactively address them. One of the things we came up with was "remove unused files from our docs repositories."

My awesome lead [@cbush](https://github.com/cbush) not only extended a tool that he wrote to work with our docs repositories to identify files that aren't used in our docs - he also set up a GitHub workflow to run this tool on every PR. So any time we make changes that result in unused files, we get a reminder to remove those files from the repo. This is above and beyond the original ask of "remove unused files."

Also, this has had the follow-on effect of causing me to prune our code example unit tests to remove unused code examples and unused tests, reducing the number of tests to maintain and making the unit test suite faster to run.

This is why #buildtogether is so powerful. One person has an idea. Another person adds to it and makes it better. Follow-on effects amplify the impact.

It's so great to work in an org where we live our values every day, and those values have a real impact on the work we do.
