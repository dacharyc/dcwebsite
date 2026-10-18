---
title: "Thanks to a contribution from @marc0olo, the skill-validator tool can now use…"
author: Dachary Carey
date: 2026-03-23 21:28:47 -0400
url: /microblog/2026/03/23/thanks-to-a-contribution-from-marc0olo-the-skill-validator/
topics: [ai, agents, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7442019551921303553
draft: false
---

Thanks to a contribution from [@marc0olo](https://github.com/marc0olo), the `skill-validator` tool can now use Claude Code in headless mode to do LLM-as-judge scoring! 🎉

Before this change, you could perform structural validation fast and free, but the qualitative LLM-as-judge scoring to assess your skill across multiple dimensions was only available with an Anthropic or OpenAI API key.

Now, if you've got a Claude Code subscription through work or your personal org, you can use your Claude CLI binary to perform the LLM-as-judge scoring.

One caveat: headless mode still loads your CLAUDE.md, skills, and other context, which can affect scoring output versus the bare API calls. But for folks who aren't as sensitive to the possible context pollution affecting scoring reproducibility, this feature makes LLM-as-judge scoring accessible without the extra cost of the API calls.

Available now in the v1.5.0 release.

And this is why I love open source 😁
