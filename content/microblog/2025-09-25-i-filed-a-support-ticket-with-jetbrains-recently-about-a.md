---
title: "I filed a support ticket with JetBrains recently about a regression"
author: Dachary Carey
date: 2025-09-25 07:42:47 -0400
url: /microblog/2025/09/25/i-filed-a-support-ticket-with-jetbrains-recently-about-a/
topics: [coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7376944256374026242
draft: false
---

I filed a support ticket with JetBrains recently about a regression. Something that used to work in an older build no longer worked in the most recent build.

They were unable to reproduce my issue. I spent some time putting together a minimum reproducible test case, and in doing so, discovered a setting that solved the issue.

I don’t know whether this was a new setting, or the upgrade process to the latest build somehow lost a setting I had selected in the past. In either event, this setting caused a change in behavior that looked to me, the user, like a bug. A JetBrains support engineer lost time trying to reproduce my issue, and I lost time setting up the test case and also changing my workflows to work around this change in behavior.

Setting defaults matter! If it causes a change in behavior, it probably should have defaulted to the case that preserved the existing behavior. If the setting changed during upgrade, that’s a miss in the upgrade process.
