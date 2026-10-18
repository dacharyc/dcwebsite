---
title: "Just finished writing some tooling to programmatically extract our hard-coded…"
author: Dachary Carey
date: 2025-04-30 12:56:11 -0400
url: /microblog/2025/04/30/just-finished-writing-some-tooling-to-programmatically/
topics: [documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7323389722528026624
draft: false
---

Just finished writing some tooling to programmatically extract our hard-coded inline code examples out to separate files which can be included in the docs.

Code examples that are hard-coded inline in your docs *may* have been tested at the time they were added, but the moment you hard-code them inline, they become decoupled from the product and untestable going forward.

- You can’t test them with new SDK or API versions.
- You miss out on basic compile testing for valid syntax.
- You can’t use linters to standardize the formatting of your code examples.

If you value accuracy, consistency, and maintainability, don’t hard-code your code examples inline in your docs!

**Links from the comments:**

- <https://github.com/cbush/docdoctor/pull/13>
