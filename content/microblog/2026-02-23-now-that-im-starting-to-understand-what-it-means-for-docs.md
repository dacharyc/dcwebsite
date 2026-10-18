---
title: "Now that I'm starting to understand what it means for docs to be agent…"
author: Dachary Carey
date: 2026-02-23 09:15:29 -0500
url: /microblog/2026/02/23/now-that-im-starting-to-understand-what-it-means-for-docs/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7431703250388553728
draft: false
---

Now that I'm starting to understand what it means for docs to be agent friendly, I'm charging full steam ahead with tooling to support this important docs consumer. If you want to check how agent-friendly your docs are, you can use this new tool I'm developing and distributing through npm: `afdocs`

It's a CLI/library that implements the Agent-Friendly Docs Spec and is distributed via npm. Currently, only the first three check groups are implemented, but I think those are some critical entry point checks that have real value today and don't want to delay release until the entire spec is implemented. You can run checks for:

llms.txt (5 checks)
 - llms-txt-exists: Can agents find your llms.txt? Checks candidate locations so agents have a starting point for your docs.
 - llms-txt-valid: Does it follow the proposed structure from llmstxt.org? Malformed files mean agents can't parse your doc index.
 - llms-txt-size: Will it fit in an agent's context window, or will it get truncated before the agent sees everything?
 - llms-txt-links-resolve: Do the URLs in llms.txt actually work? Broken links send agents into dead ends.
 - llms-txt-links-markdown: Do those links return markdown? Agents work much better with markdown than raw HTML.

 Markdown Availability (2 checks)
 - markdown-url-support: Can agents get markdown by appending .md to page URLs? This is the simplest way to serve agent-friendly content.
 - content-negotiation: Does the server respond to Accept: text/markdown? A standards-based alternative to URL conventions.

 Page Size (3 checks)
 - page-size-markdown: Are your markdown pages small enough for agents to consume without truncation?
 - page-size-html: How large are your HTML pages, and how much shrinks away when converted to markdown? High boilerplate ratios waste agent context or may mean docs are summarized instead of read verbatim.
 - content-start-position: How far into the page does the actual content start? Navigation, headers, and boilerplate at the top push real content past truncation limits.

That's the 10 implemented checks. The remaining 11 are stubbed across content structure, URL stability, agent discoverability, observability, and authentication categories. I'll be continuing to build these out in the coming weeks.

Link to package on npm: https://www.npmjs.com/package/afdocs
