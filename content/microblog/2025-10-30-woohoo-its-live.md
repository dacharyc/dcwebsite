---
title: "Woohoo, it's live!"
author: Dachary Carey
date: 2025-10-30 08:34:04 -0400
url: /microblog/2025/10/30/woohoo-its-live/
topics: [documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:share:7389640734821273601
draft: false
---

Woohoo, it's live! For all my documentarian pals who manually maintain OpenAPI specs, here's a tool you might find useful: OASprey, a package that lets you use the Jest testing framework to validate that real API calls match your API spec.

In this screenshot, we're looking at a Jest test file where we load an OpenAPI spec from our docs, call various endpoints, and assert that the status is 200 and the response we get satisfies the API spec. You can either load the published spec from a URL or the pre-published spec from a local file, depending on your needs.

Each test fails if we get fields or types in the actual response that do not match the fields and types in the API spec. By running the tests regularly, we can validate that the underlying API we've documented still matches the spec. If new fields are added, types are changed, or other breaking changes happen, failing tests will alert us that we need to update the API spec.

This isn't net new - it's a hard fork and modernization of an abandoned utility - but now it's available via npm. Links in comments. Happy testing!

**Links from the comments:**

- <https://www.npmjs.com/package/oasprey>
- <https://github.com/grove-platform/oasprey>
