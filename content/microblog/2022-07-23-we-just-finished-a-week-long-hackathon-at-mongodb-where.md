---
title: "We just finished a week-long hackathon at MongoDB where employees are…"
author: Dachary Carey
date: 2022-07-23 12:37:17 -0400
url: /microblog/2022/07/23/we-just-finished-a-week-long-hackathon-at-mongodb-where/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:6956648444602028032
images:
  - src: /images/microblog/2022-07-23-we-just-finished-a-week-long-hackathon-at-mongodb-where.jpeg
    alt: "Screenshot of a GitHub pull request in a readability demo repo. A github-actions bot comment reports Flesch Reading Ease scores for the changed documents, 74.79 for source/sdk/swift/install and 62.54 for source/sdk/node/install, followed by a reference table mapping score ranges to difficulty from 90 to 100 Very Easy down to 0 to 29 Very Hard. Below it, the checks section shows \"All checks have passed\" with one successful check named \"Readability / Parse text, score text, and report scores on PR\"."
draft: false
---

We just finished a week-long hackathon at MongoDB where employees are encouraged to cancel meetings and put aside regular work to focus on hackathon projects. For this hackathon, I led a team that implemented a GitHub Workflow to rate the readability of files we touch in documentation PRs, and report those scores as comments on the GitHub PRs.

As the hackathon closes, we have a working demo! This is very close to being able to use it in our documentation repositories as-is to give writers info about how readable our docs pages are. I need to do a little more tweaking of the script that traverses rST nodes and outputs plain text, in order to improve the accuracy of the scores, but it is an awesome tool to have for our documentation teams.

In the future, I'm hoping to add more info, like a delta in the score between main and the PR branch - i.e. does this PR improve or decrease the readability score - and apply additional heuristics so we can get a more well-rounded view of a page's readability.

I love being a part of an organization that takes time out of regular work to encourage employees to work on interesting or helpful projects, and being part of a team that can build tools like this that can potentially make a positive impact across our entire docs organization.
