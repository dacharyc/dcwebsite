---
title: "I’ve started reviewing some draft agent skills, and am learning a few things…"
author: Dachary Carey
date: 2026-03-11 07:39:45 -0400
url: /microblog/2026/03/11/ive-started-reviewing-some-draft-agent-skills-and-am/
topics: [ai, agents]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7437462262925123584
draft: false
---

I’ve started reviewing some draft agent skills, and am learning a few things about optimizations we can make:

- If a skill has prerequisites that are implicitly stated somewhere in the skill body, move them to the top and explicitly state them as prerequisites. Provide a scope gate with instructions for how the agent should handle missing prerequisites. Should it prompt the user? Attempt to install something on its own? Take some local action to resolve the missing prerequisites?
- If a SKILL.md contains info that is only needed conditionally, consider moving it out of the skill body and into a reference file in ‘references/‘. Agents load the entire skill file into context every time it’s invoked, and if part of the content is not required for every invocation, you’re wasting context with information that only applies conditionally.
- If your skill relates to something that’s behind a feature flag, consider what happens if the feature flag is removed. Is the skill written in such a way that it’s still useful? Are there instructions for the agent about what to do once the feature flag is no longer there? Will it the skill need to be updated or removed, and do you have a way to let your users know when that needs to happen?
- Is your skill or any reference content tied to information that is maintained somewhere else? If so, changing the information in the other source should trigger a skill update. You need to have processes in place to watch external sources of truth and alert you when skills need to change.

If you’ve been creating or reviewing agent skills recently, any other optimizations you’ve spotted that we should be thinking about? 🤔
