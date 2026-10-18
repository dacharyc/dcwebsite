---
title: "This one is for all of my tech writing friends who have ever had to hand-write…"
author: Dachary Carey
date: 2025-08-18 22:16:54 -0400
url: /microblog/2025/08/18/this-one-is-for-all-of-my-tech-writing-friends-who-have/
topics: [documentation, personal]
linkedin: https://www.linkedin.com/feed/update/urn:li:ugcPost:7363393496054992897
images:
  - src: /images/microblog/2025-08-18-this-one-is-for-all-of-my-tech-writing-friends-who-have-1.jpeg
    alt: "Code screenshot of a Jest test file loading an OpenAPI spec from a local file. It imports jestOpenAPI from 'jest-openapi' and axios, sets baseURL to https://status.mongodb.com/, calls jestOpenAPI(\"path/to/openapi-source.json\"), then in a describe block titled \"OpenAPI spec to fetch status should work\" has two tests, \"should match the get summary schema\" and \"should match the get status schema\". Each one GETs api/v2/summary.json or api/v2/status.json, expects status 200, and calls expect(res).toSatisfyApiSpec()."
  - src: /images/microblog/2025-08-18-this-one-is-for-all-of-my-tech-writing-friends-who-have-2.jpeg
    alt: "Code screenshot of the same Jest test file loading the OpenAPI spec from a remote URL instead: a beforeAll hook fetches the spec with axios.get from a MongoDB docs API URL, reads response.data, and passes it to jestOpenAPI(openApiSpec), followed by the \"should match the get summary schema\" test that GETs api/v2/summary.json, expects status 200, and calls expect(res).toSatisfyApiSpec()."
  - src: /images/microblog/2025-08-18-this-one-is-for-all-of-my-tech-writing-friends-who-have-3.jpeg
    alt: "Terminal screenshot of the Jest test run for tests/status.test.js marked PASS. Under \"OpenAPI spec to fetch status should work\", eight tests pass with green check marks: the get summary, get status, get components, get unresolved incidents, get all incidents, get upcoming scheduled maintenances, get active scheduled maintenances, and get all scheduled maintenances schemas, each in under about 300 ms. The summary reads 1 test suite passed, 8 tests passed, 0 snapshots, 2.845 seconds."
draft: false
---

This one is for all of my tech writing friends who have ever had to hand-write and maintain OpenAPI spec manually...

I've forked and updated an abandoned library that can test OpenAPI endpoints trivially easily.

You load the OpenAPI spec - either from a local file, or from a remote URL, such as a live docs page.

Then you call a live endpoint, and use test framework assertions to validate that you get the expected status code and that the API response matches the schema that your OpenAPI spec has defined for that endpoint/status.

All with a few lines of code.

This is definitely going into my toolkit!
