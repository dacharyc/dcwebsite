---
title: "I'm really excited to share what my team has been working on - tooling to…"
author: Dachary Carey
date: 2025-11-13 08:35:13 -0500
url: /microblog/2025/11/13/im-really-excited-to-share-what-my-team-has-been-working-on/
topics: [documentation]
linkedin: https://www.linkedin.com/feed/update/urn:li:ugcPost:7394729552138104832
images:
  - src: /images/microblog/2025-11-13-im-really-excited-to-share-what-my-team-has-been-working-on-1.jpeg
    alt: "Code snippet from the old test suite: five Jest assertions that check individual values one at a time. expect(tasks.length).toBe(3); expect(openTasks.length).toBe(3); expect(tasksByName[0].name).toBe(\"go exercise\"); expect(tasksByName[1].name).toBe(\"go grocery shopping\"); expect(tasksByName[2].name).toBe(\"wake up\");"
  - src: /images/microblog/2025-11-13-im-really-excited-to-share-what-my-team-has-been-working-on-2.jpeg
    alt: "Code snippet from the new testing paradigm: the result of await runTimeFieldQuery() is compared against an expected output file at time-series/quick-start/quick-start-time-field-output.sh using a single fluent call, Expect.that(result).withUnorderedSort().shouldMatch(outputFilepath);"
draft: false
---

I'm really excited to share what my team has been working on - tooling to transform how writers test code examples in documentation!

On the left, we have a snippet from one of our old test suites. We used the same tools that developers did to execute code and validate the results. But this required you to *be a developer* - or at least have near-developer-level-knowledge of testing and testing frameworks. So this tooling was only accessible to a small subset of writers at our org.

On the right, we have a snippet from our new testing paradigm that we're currently rolling out across the org. It abstracts away the testing piece and gives writers a simple way to validate that their code executes successfully and produces the expected results.

They write their code example in a function they can call in a test, create an expected output file that we can show in the docs, and use our comparison utility to validate that the result from executing the code matches the contents of the expected output file. No need to master the individual assert syntax of every testing library in every language we support. No need to write a million individual asserts to validate every value of every object in the output. Just one unified API that works the same across every language.

I'm really proud of the work my team has done here, and will be writing a series of blog posts about what it took to get here. But for now, you can see the whole writer workflow in the new docs we've rolled out for our writing team - link in the comments.

I'm really curious how other orgs handle testing the code examples that they show in their docs. If your org does testing, how do you do it?

**Links from the comments:**

- <https://www.mongodb.com/docs/meta/grove/>
