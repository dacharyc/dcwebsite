---
title: "Your Agent Skill isn't the same on every platform"
author: Dachary Carey
date: 2026-08-03 08:14:07 -0400
url: /microblog/2026/08/03/your-agent-skill-isnt-the-same-on-every-platform/
topics: [ai, agents, documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7490017153882959874
images:
  - src: /images/microblog/2026-08-03-your-agent-skill-isnt-the-same-on-every-platform.jpeg
    alt: "Screenshot of the Agent Skill Implementation website home page in dark mode. The headline reads \"The same skill behaves differently on every platform\", with a summary explaining that Agent Skills promise write-once portability across 25+ platforms and the site measures where that promise holds and where it breaks, using the same probe skills run on real platforms with every finding cited to a transcript. A sample platform comparison for the bundled-script-execution check asks whether the agent can run a bundled scripts/ file: Antigravity CLI and Codex CLI ran the script and returned output, while Claude Code blocked it with an error. A footer line notes 40 checks, 3 platforms, every claim transcript-cited. Three cards below describe The Checks, Platform Reports, and Authoring Guidance, and a section titled \"One skill, different outcomes\" begins with cards for frontmatter vanishing, grouped skills disappearing, and names resolving differently."
draft: false
---

Your Agent Skill isn't the same on every platform. The Agent Skills spec is un-versioned and under-specified. Harnesses may have implemented support for skills before official implementation guidance landed, and may not have kept up with spec guidance. And that means that each harness may have its own story of how it supports skills, with no way to communicate to users what that looks like.

I've automated 38 of my 40 checks, with every finding cited to an archived transcript rather than the model's self-report, and I can now tell you that there are observable differences in how Antigravity CLI, Claude Code, and Codex CLI support skills, including:

- Antigravity and Codex CLIs use the skill name from the frontmatter in SKILL.md, while Claude Code uses the directory name. If the names don't match, your skill may answer to different names on different platforms, potentially breaking docs references or references in other skills.
- Claude Code strips frontmatter entirely. Aside from its name-and-description-only listing, other frontmatter fields such as compatibility notes, metadata, and any custom fields you add never reach the model through any platform channel.
- Name collisions contradict the official guide. The implementation guide calls project-over-user precedence "a universal convention". But Claude Code resolves user-over-project, and Codex doesn't resolve it at all (the model picks).

For a full breakdown of how each platform handles skills, including which ones are spec compliant and where they diverge, as well as authoring guidance based on my findings, check out the updated site.

**Links from the comments:**

- <https://agentskillimplementation.com>
- <https://agentskillimplementation.com/platforms/>
- <https://agentskillimplementation.com/guidance/>
