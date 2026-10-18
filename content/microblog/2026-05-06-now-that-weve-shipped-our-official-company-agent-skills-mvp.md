---
title: "Now that we’ve shipped our official company Agent Skills MVP, I am working on…"
author: Dachary Carey
date: 2026-05-06 12:18:01 -0400
url: /microblog/2026/05/06/now-that-weve-shipped-our-official-company-agent-skills-mvp/
topics: [ai, agents, documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7457826013608095744
draft: false
---

Now that we’ve shipped our official company Agent Skills MVP, I am working on what it looks like to mature our skill development process. I’m still forming my thoughts around this but curious how much of this resonates with what other orgs are thinking:

- Vibe-coded skills pollute an org’s namespace for dubious gains. I’m implementing a gating process that requires prompts & evals across model families to determine whether a skill is needed and can actually solve the problem as presented - BEFORE writing the skill.
- A skill author must be able to articulate the need clearly through prompts and what we expect the model to produce or where we expect it to fail, or you get a poorly-scoped skill that doesn’t quite know what problem it’s trying to solve.
- For official company-distributed skills, these are part of the product surface area and should be owned as such.
- Not everything can or should be solved with a skill. For some things, the fixes are docs improvements, API changes, hooks/rules or other AI artifacts, or larger cross-cutting initiatives. These resources need an owner that understands the product surface area and which lever is the right one.

More broadly, officially company-distributed AI tools and resources in general are an emerging category. It started with companies deciding they need MCP servers, and building them as one-off agent access planes. But now there is an emerging suite of agent resources that warrant a clear company strategy and ownership. Along with study of how agents use your product suite and where the sharp edges are that need product-level improvements, not just resource bandaids.

I’m picturing something like content strategy, but instead of content, the surface is agents and optimizing for all the ways they interact with your products.

I think we’re watching the birth of Agent Experience in real time, as a distinct but complimentary category alongside Developer Experience.

Curious how other folks are seeing things play out in their orgs. Do you have maturing strategies for this stuff? Are you handling everything ad-hoc and just trying to keep up? The challenge here is compounded by the speed of movement in the AI space right now.
