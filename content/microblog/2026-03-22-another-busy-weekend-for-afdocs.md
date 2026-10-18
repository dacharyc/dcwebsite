---
title: "Another busy weekend for afdocs"
author: Dachary Carey
date: 2026-03-22 23:04:10 -0400
url: /microblog/2026/03/22/another-busy-weekend-for-afdocs/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7441681166413377536
draft: false
---

Another busy weekend for `afdocs`. The tool is up to v0.7.1 with fixes and improvements:

- Many fixups for the `markdown-content-parity` check - turns out, comparing HTML and markdown is complicated! I'm sure there's more to do there.
- Several improvements for running the tool's checks in CI:
   - Implement the `describeAgentDocsPerCheck` vitest helper to get a detailed per-check breakdown in CI
   - Improvements for config resolution and default timeouts
   - A copyable example setup showing you exactly what files you need to set up the tests and CI in your project

I now have my personal website and my 4 websites for agent-related stuff all fully passing the agent-friendly docs checks! 🎉 And CI is set up to run the checks on every push or PR to `main` for every site to make sure they stay that way as I continue to add content.

I also did find that the `markdown-content-parity` check helped me identify a lot of missing content from my markdown pages. So now the markdown templates I'm using for Hugo are much more complete. 🎉

And anecdotally, while Claude was trying to identify the missing content to fix up some of my pages, it kept attempting to fetch the HTML versions of the page and being served the markdown due to content negotiation 😁 It's exactly what you want in regular usage, because the markdown is much cleaner for agents, but I had to keep telling it to curl the pages instead of fetch them as a result 😉
