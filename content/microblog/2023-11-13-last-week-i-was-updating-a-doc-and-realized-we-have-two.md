---
title: "Last week, I was updating a doc and realized we have two very similar-sounding…"
author: Dachary Carey
date: 2023-11-13 17:28:32 -0500
url: /microblog/2023/11/13/last-week-i-was-updating-a-doc-and-realized-we-have-two/
topics: [documentation, coding]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7129958239303069696
draft: false
---

Last week, I was updating a doc and realized we have two very similar-sounding APIs:

- writeAsync
- asyncWrite

One existed before Swift introduced its async/await syntax, and used callbacks.

The other was added to support async/await syntax.

1) Someone noticed this in the docs and was confused about which one to use. Which made me realize I need to do a better job disambiguating these in the docs.
2) I bolted on a lot of async/await stuff and it may be time for a holistic revisit of async functionality.

This is a good reminder of WHY you have to actively maintain docs. It’s very easy to add or update docs in one place, and miss resulting changes in other places.

I haven’t found a better tool for this than regular audits and gap analysis. But to do that effectively requires someone with good subject matter expertise who can spot things that have changed and need updates.

Tech writers, do you have good process around this? What is it?
