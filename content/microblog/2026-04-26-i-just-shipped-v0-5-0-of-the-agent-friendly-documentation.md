---
title: "I just shipped v0.5.0 of the Agent-Friendly Documentation Spec and updated the…"
author: Dachary Carey
date: 2026-04-26 23:18:50 -0400
url: /microblog/2026/04/26/i-just-shipped-v0-5-0-of-the-agent-friendly-documentation/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7454368434391257088
draft: false
---

I just shipped v0.5.0 of the Agent-Friendly Documentation Spec and updated the AFDocs scoring tool to match. Major thanks to all the community members who participated in discussions, filed issues or PRs on the spec or the tool, and helped shape the improvements and bug fixes!

If you've been tracking your documentation site's agent-friendliness scores, they're going to change. The scoring is more accurate now, and "more accurate" could be an upside or a downside for your site.

In smoke testing across sites at every grade level, I saw:

- Every site's content discoverability score dropped. The tool now checks whether your llms.txt directive appears in both HTML and markdown formats separately, instead of lumping them together. Most sites only have it in one format, so that's a new failure where you might have gotten a pass or a warning.
- Page size and content start position scores generally improved. The tool now strips scripts and styles before measuring, matching recent updates to how Claude Code processes your pages.
- One major enterprise docs site went up a full letter grade (D to C) because they've added real markdown URL support since I last ran the tool against their site. Their llms.txt links changed from .html to .md, and their server now returns actual text/markdown at those URLs. While I haven't spoken directly with anyone there, the timing lines up with the spec's publication and the tool's growth in visibility, so I'd love to think they found the tool helpful and have been making improvements. Wins like this are good news for docs *and* for developers using agents.

Overall scores moved between -10 and +8 points. Most changed by 5 or fewer. Only 2 of 6 sites changed grade letters, one up and one down.

If you're using afdocs, I’ll throw a link to the migration guide in the comments with details about the scoring changes and what to expect.

**Links from the comments:**

- <https://afdocs.dev/migration/v0.17.0>
