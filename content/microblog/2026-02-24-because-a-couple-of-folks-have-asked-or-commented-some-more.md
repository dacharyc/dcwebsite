---
title: "Because a couple of folks have asked or commented, some more thoughts related…"
author: Dachary Carey
date: 2026-02-24 06:35:13 -0500
url: /microblog/2026/02/24/because-a-couple-of-folks-have-asked-or-commented-some-more/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7432025303432048641
draft: false
---

Because a couple of folks have asked or commented, some more thoughts related to the markdown content that features heavily in the Agent-Friendly Docs Spec:

- Where I talk about markdown in the spec, I'm talking about it as an *output* format for your docs pages. The input format doesn't matter so much. You can write in reStructuredText, MDX, or whatever format makes the most sense for your docs. But agent-friendly docs offer markdown as an output format for the docs pages to be consumed by agents, which work far better with markdown than HTML.
- I am not proposing that you maintain a second *source* that's in markdown alongside whatever source you already maintain. Ideally, your build pipeline automatically generates markdown versions of docs pages.
- Given that I expect the output markdown to be auto-generated, this is a big part of why I proposed the markdown/HTML parity check in the Observability section of the spec. I have seen in my investigation real-world cases where the markdown generation process is stripping text on the markdown versions of the pages that is rendering correctly on the human-viewable HTML versions of the pages. This includes things like parameter names, contextualizing headers and intro sections, and other information that the agent *needs* and isn't getting. The failure mode is this: human looks at page rendered in HTML, content is there, human doesn't look at markdown page and doesn't notice content is missing.

There are probably more things related to this that I'll discover as I continue to dig deeper. But I'm curious if any of y'all have started looking at your own generated markdown as a result of the spec and these convos and noticed anything else we should be thinking about?
