---
title: "Two more checks implemented in v0.4.0 of the afdocs npm package!"
author: Dachary Carey
date: 2026-03-12 07:58:55 -0400
url: /microblog/2026/03/12/two-more-checks-implemented-in-v0-4-0-of-the-afdocs-npm/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7437829477575069696
draft: false
---

Two more checks implemented in v0.4.0 of the `afdocs` npm package! 🎉 This brings the tool up to 16 of 21 checks implemented.

If you haven't seen it yet, `afdocs` is a TypeScript CLI and library available through npm to help you check if your docs are agent-friendly. It implements checks against the Agent-Friendly Docs Spec, giving you immediate signal on where your docs are already agent-friendly, and where you could make enhancements.

The two new checks I've added in this version are:

- `redirect-behavior`: checks how redirects are implemented in your docs, and warns or errors if they're implemented in a way where agents may not be able to follow them
- `llms-txt-directive`: checks both your HTML and markdown pages to see if they have a hidden directive referring agents to an llms.txt file. This can help agents find agent-friendly content, like markdown instead of HTML, or serve as an index for agents to find related or relevant pages

If you've got Node.js/npm installed, you can run the tool on any docs site today with this command:

`npx afdocs check https://docs.example.com`

Just change the `docs.example.com` link to your docs site and give it a try! It samples random pages to give you info about how agent-friendly your docs are. You can specify how many pages to sample with the `--max-links` option; it defaults to sampling 50 pages.

You can also use it as a library to help define tests for agent-friendliness that you can run in CI.

I'll throw a link to the package on `npm` in the comments if you'd like to learn more.

**Links from the comments:**

- <https://www.npmjs.com/package/afdocs>
