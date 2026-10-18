---
title: "Technical writing is where implementation validates design"
author: Dachary Carey
date: 2026-06-02 08:10:06 -0400
url: /microblog/2026/06/02/technical-writing-is-where-implementation-validates-design/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7467548094780563456
draft: false
---

Technical writing is where implementation validates design. AI can’t do it.

Let me tell you a tale of my latest project at work…

A teammate and I are building an Agent Skill Dashboard. It’s a state management tool that aggregates information from many different sources to provide a single record of truth about the state of any given official skill, and to manage processes around idea submission, update requests, and to provide a triage team a tailored view of requests.

I wrote a design document to capture how the process should work. I sketched out details about the end-to-end process, what each actor should see and the actions they should be able to take, and how systems would talk to each other.

Then we implemented it. Discussions happened. We made decisions that diverged from the design.

After building it, I had AI draft process docs for users to use the system. It looked at both the codebase and the design document. It wrote docs that look reasonable at a high level, but every single one has outdated information that doesn’t correctly track either source. Sometimes it pulls in info from the design that didn’t get implemented. Sometimes it pulls in information from implementation but misinterprets what it found. It’s not consistently skewed in either direction, so it can’t easily be corrected with a second pass.

It also records information without understanding what a human needs to know, and why. It documents internal design rationale that isn’t relevant for users following the process. It created a helpful glossary of terms on one page that span all 6 pages, and a user who only views page 5 will never see it. It repeats UI details verbatim, which isn’t helpful - a user can get those details from the UI - and omits the things a user needs to know about where to find or how to create relevant values.

Every technical writer knows these gaps well. It’s exactly this reconciliation that forms the bulk of our work. Figuring out what decisions were made, and why, and what matters for users, when those decisions were never written down - this is the “detective mode” we live in. And no AI system or context augmentation platform or memory harness can capture these details because they either live in someone’s head, or were decided in conversations that nobody wrote down.

So I will spend a few days this week fixing up the AI documentation draft.

And orgs that don’t have people capable of doing this work are going to learn the hard way what happens when they’re missing this expertise.
