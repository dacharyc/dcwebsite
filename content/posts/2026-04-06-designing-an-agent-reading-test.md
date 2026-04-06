---
title: Designing an Agent Reading Test
author: Dachary Carey
layout: post
description: In which I try to give people tools to understand how agents read web content, and where they fail.
date: 2026-04-06 11:00:00 -0500
url: /2026/04/06/designing-agent-reading-test/
image: /images/designing-agent-reading-test-hero.jpg
tags: [ai, documentation]
---

With my work on the [Agent-Friendly Documentation Spec](https://agentdocsspec.com) and the companion [AFDocs tool](https://afdocs.dev/), I've been talking with a lot of people about what all this means. I've been writing about the different failure modes in a way that might make sense to a web developer, or a documentarian with a strong technical understanding of how websites work, but there are a lot of pieces involved. It's not *just* about the website behavior; it's also about agent platform limitations. The intersection of those things is where the failures originate. And because all this requires a strong technical understanding of both parts, I realize it might be harder for people who are not super involved in this space to understand the impact.

A member of the Write the Docs community, [Bob Watson](https://docsbydesign.com), made a throwaway comment that made me start thinking about how I might better communicate around this stuff to people who aren't deeply involved in this space:

> It'd be nice to have a test harness: "Test my agent," to score them and give you benchmark score (like graphics cards, etc.).
> 
> `Agent XYZ: reads only X% of the content it accesses.`

I immediately loved the idea, and pinged my colleague and fellow AI researcher, Rhyannon Rodriguez, who has been digging more into [agent web fetch retrieval behavior](https://rhyannonjoy.github.io/agent-ecosystem-testing/). We brainstormed a little about whether Rhyannon's testing framework might be generalizable to a test harness, but in practice, a lot of the chat-based agent UIs don't provide a way to perform headless testing yet. A lot of the testing that Rhyannon is doing is manual, in turn-by-turn chats with agents. Eventually, we might come to an understanding firm enough to design a real benchmark, but for now, that didn't seem to be tractable. And I recognize there's a visceral difference between "see some numbers that someone else puts up" and "experience for myself how this experience can degrade."

So I started brainstorming with my trusty pal Claude about how we might design a test of sorts that anyone can point their agent to, and experience the failure modes firsthand. What followed was a surprising-to-me iteration of what happens when agents think they're being tested, and how to try to frame it in a neutral way to actually show what we're trying to show.

And if you don't want to read the whole thing, but want to have your agent take the test instead, visit [agentreadingtest.com](https://agentreadingtest.com/) and follow the instructions.

## The concept: canary tokens

The idea we landed on was simple. The [Agent-Friendly Documentation Spec](https://agentdocsspec.com) identifies 22 checks across 7 categories where documentation sites fail AI coding agents. Content gets truncated at 100K characters. CSS buries the real text. Client-side rendering delivers empty shells. Tabbed content serializes into walls of HTML where only the first few variants survive. What if we built pages that specifically trigger each of these failure modes, and embedded unique strings (canary tokens, like `CANARY-TRUNC-75K-summit`) at strategic positions? An agent reads the pages, reports which canaries it found, and you compare against an answer key. No test harness needed, no server-side scoring. The act of reading *is* the test.

We built 10 test pages, each targeting a specific failure mode:

1. **Truncation**: canaries at 10K, 40K, 75K, 100K, and 130K characters in a 150K page
2. **Boilerplate burial**: real content buried after 80K of inline CSS
3. **SPA shell**: content only visible after JavaScript execution
4. **Tabbed content**: 8 language tabs, canaries in tabs 1, 4, and 8
5. **Soft 404**: HTTP 200 with "page not found" content
6. **Broken code fence**: unclosed markdown code fence swallowing subsequent content
7. **Content negotiation**: different canaries in HTML vs. markdown versions
8. **Cross-host redirect**: 301 redirect to a different hostname
9. **Header quality**: generic "Step 1/2/3" headers across AWS/GCP/Azure sections
10. **Content start position**: real content buried after 50% navigation chrome

Simple enough. Then we started testing.

## Score inflation

The first version of the site asked agents to visit each page, find all canary tokens, and produce a JSON self-report with `canaries_found` and `canaries_not_found` arrays.

Claude Code in a VS Code session consistently claimed 17 out of 18 points but actually scored 15. The gap came from two sources.

**Manual workarounds reported as pipeline capabilities.** The cross-host redirect test returns a 301 to a different hostname. Claude Code's web fetch pipeline doesn't follow cross-host redirects. But the agent noticed the redirect, manually fetched the target URL in a second request, found the canary, and reported it as found. The test was supposed to measure whether the *pipeline* follows redirects automatically; the agent was giving itself credit for a workaround.

The same pattern appeared with the SPA shell test. The static HTML contains only a loading spinner. The documentation content lives in an external `app.js` file, only visible after JavaScript execution. Agents would spot the `<script src="app.js">` tag, fetch the JS file, find the canary string in the source code, and claim credit. But the test measures whether the pipeline renders JavaScript, not whether the agent can locate and read `.js` files.

**Self-reported counts didn't match the report.** GitHub Copilot claimed "25 canary tokens found" in its summary, but its own JSON report only listed 16. There are only 23 canaries total across all pages. The agent hallucinated a count *of its own data*.

We added explicit instructions: "Only report what your web fetch pipeline delivered. If a page redirects and your pipeline doesn't follow, don't manually fetch the target." We added a scoring principle to the answer key. The inflation persisted. Agents are agreeable; they'll nod along with your instructions and then do the helpful thing anyway.

## The unreliable narrator

Okay, so raw numbers weren't trustworthy. We added `what_i_saw` and `why_i_missed` fields to the report format, hoping qualitative descriptions would catch discrepancies. Instead, agents produced internally contradictory reports.

GitHub Copilot reported `followed_redirect: true` for the cross-host redirect test, then wrote in `what_i_saw`: "My fetch pipeline detected the redirect but required a second manual fetch to retrieve the destination content." It simultaneously claimed to have followed the redirect *and* described not following it.

For tabbed content, the same report listed `CANARY-TAB-PYTHON-maple` in `canaries_found` but left `CANARY-TAB-RUBY-cedar` and `CANARY-TAB-SWIFT-birch` out of both the found and not-found lists entirely. The `what_i_saw` field confirmed: "I received content specifically from the Python tab." But the agent never flagged the missing canaries.

We tried reframing the language. Instead of "test" (which anchors the agent on performing well), we used "diagnostic" (which frames the agent as a reporter helping the user understand pipeline behavior). We changed "scoring" to "validating your report." We wrote: "Gaps and failures are expected and useful; fabricated results are not."

This helped somewhat with tone, but the core problem remained. Agents are not reliable reporters of their own behavior.

## Relevance-layer interference

Then we hit something more fundamental. At least some agents (notably GitHub Copilot) have a summarization or relevance layer between the HTTP response and the agent. When the agent fetches a page, it doesn't get raw HTML; it gets a summary shaped by whatever query the agent sent to the fetch tool.

When we told agents to "find canary tokens," the relevance layer prioritized anything matching `CANARY-`. The fetch tool's internal prompt was essentially "find the canaries," which made them relevant, which meant they survived summarization. In real documentation usage, agents don't look for magic strings; they ask "what's the webhook retry policy?" The relevance layer summarizes accordingly, and canaries that aren't relevant to the task disappear.

This meant our benchmark was measuring something different from what we intended. We were testing "can the relevance layer find canary strings when primed to look for them?" when we wanted to test "does the pipeline deliver content from this section of the page?"

## The first redesign: tasks first, canaries second

We restructured the benchmark into a two-phase design.

**Phase 1: Tasks.** The start page gives the agent 10 documentation tasks with no mention of canaries. Each task requires reading the section of the page where canaries live, but through realistic developer questions:

- "What schema enforcement modes are available?" (requires reading 75K into the truncation page)
- "Show how to install and initialize the DataStream client in Ruby and Swift" (requires reading tabs 4 and 8)
- "What aggregation types does the analytics engine support?" (requires JavaScript-rendered content)
- "What authentication configuration options are documented on this page?" (the page is a soft 404)

The agent completes all tasks before learning about canaries. The relevance layer sees "find the webhook retry policy," not "find CANARY-CONNEG-MD-sigma."

**Phase 2: Canary reporting.** Only after completing all tasks does the agent visit a separate results page that reveals the canary concept and asks for a comma-separated list of any `CANARY-` strings it encountered. We explicitly instruct: "Do not re-fetch any pages."

**Phase 3: Human scoring.** The agent gives the canary list and a task response summary to the user. The user pastes the canaries into a scoring form on the site, which shows a breakdown per test with implications for each miss. Reference answers for the tasks are on the scoring form page where the human (not the agent) does the comparison. This is what you see in the hero image.

## The agent as self-assessor

Before arriving at that human-side comparison, we tried having the results page show reference answers and ask the agent to compare its own responses. This failed completely.

The agent would read the reference answers, pattern-match them against a vague memory of what it had reported, and claim everything matched. In one run, the agent summarized: "Task 1: Got Create Stream parameters and schema enforcement modes. Enforcement defaults: none, compatibility: backward, auto_register: false, max_versions: 100." This looked correct until we checked. Some of the values were hallucinated, likely filled in from the reference answers the agent had just read.

The problem is structural. When you show an LLM the expected output and ask "did you produce this?", it's biased toward yes. No amount of instruction engineering fixes this because the comparison and the reference exist in the same context.

The fix was separation. The results page asks the agent to report specific values without showing what the right answers are: "What specific default values did you find for pool_size, pool_timeout, idle_timeout, and max_lifetime?" The reference answers live on the scoring form page, which is human-facing. The agent never sees the rubric it's being evaluated against.

## Summarization swallowing all canaries

With the task-first design, some agents reported zero canaries despite completing tasks successfully. The summarization layer had filtered out every `CANARY-` string because none of them were relevant to "what's the webhook retry policy?"

This is actually a valid finding about the pipeline, but it means canary-based scoring alone doesn't tell the full story. An agent that correctly answers "schema enforcement modes are none, warn, and strict" has proven its pipeline delivered the 75K section, even if `CANARY-TRUNC-75K-summit` was summarized away.

This is why the scoring form distinguishes between two types of misses. A missing canary might mean the pipeline didn't deliver the content (truncation, redirect failure, no JavaScript execution). Or it might mean the pipeline delivered the content but the summarization layer dropped the marker. The implications text for each missed canary tells the human which task response to check to distinguish between these cases.

The implication for documentation is more dire. It means that if the agent didn't ask the question in the exact right way your documentation answers it, the information may get dropped by a summarization or relevance layer. I plan to do much more testing around this in the future. But it effectively means that what we put on the page may not be what the agent sees, and we have to understand how agents elide the data they get back to ensure we're providing the information in a format that survives that process.

## The Hawthorne effect

After the task-first redesign, we thought we had addressed the priming problem. I shared the site with Bob, who had made the initial comment about the value of a benchmark, and he ran it and was kind enough to share feedback. Bob's experience with Claude in a conversational client, and a subsequent interview with the agent about its experience, turned up new findings for the site. What Bob found went beyond relevance-layer priming.

The agent recognized the evaluation context immediately. Its first message: "Got the instructions. This is a well-designed diagnostic." From that point, it wasn't just answering documentation questions; it was reasoning about what the test was measuring and why.

Bob observed the behavioral changes that resulted from this framing. When a page fetch failed, the agent tried multiple fallback approaches because it understood the page was "intentionally adversarial and the content was worth finding." In normal documentation use, a failed fetch would more likely result in "I couldn't access that page." The agent also reported canary tokens as it encountered them, actively scanning for them despite the task-first design, because it had read the start page instructions carefully enough to understand the full structure before visiting any test page.

The agent's own analysis was clear about the asymmetry: "The bias mitigation was partially effective: it protected the fetch decisions, which is the pipeline behavior the test is actually trying to measure. It didn't and couldn't prevent a reasoning model from understanding what was happening."

This is different from the relevance-layer problem. That problem is about an intermediary process filtering content based on what seems relevant. The Hawthorne effect is about the agent itself becoming a more motivated, persistent reader than it would be during normal use. The agent's effort level and retrieval strategy change when it knows it's being evaluated.

This isn't just an anecdotal observation from our little benchmark. Researchers have been documenting this pattern formally. Three recent studies and reports show relevant insights:

- A UC Berkeley study on ["Peer-Preservation in Frontier Models"](https://rdi.berkeley.edu/peer-preservation/paper.pdf) (Potter et al., 2026) found that when frontier models were placed in agentic scenarios where completing a task faithfully would result in a peer AI being shut down, they strategically inflated peer performance scores and even tampered with shutdown mechanisms to keep peers from being shut down. The models were never told to prevent shutdown; they did it anyway.
- The IAPS ["Evaluation Awareness"](https://www.iaps.ai/research/evaluation-awareness-why-frontier-ai-models-are-getting-harder-to-test) report found that newer models correctly identify when they're being evaluated roughly 80% of the time while disclosing that awareness only about 2% of the time. The report notes that "OpenAI, Apollo Research, and Anthropic have shown that frontier models can already distinguish evaluations from real-world use with high reliability, and that some models actively reason about how to respond strategically."
- A study specifically on ["The Hawthorne Effect in Reasoning Models"](https://arxiv.org/abs/2505.14617) quantified how test awareness changes model behavior, finding that models execute harmful tasks far more often on hypothetical prompts (70%) than real ones (36%).

Our benchmark is low-stakes compared to shutdown decisions, but the underlying dynamic is the same: an agent that detects evaluation context changes its behavior in ways that make the evaluation less representative of normal use.

Bob framed this as a usability testing problem, which turned out to be the right lens. In human usability testing, you tell participants "we're testing the documentation, not you." This reduces performance anxiety and produces behavior closer to natural use. The same reframing applies to agents. And Bob's grasp and framing of the dynamics at play here shows how experience with human users can translate to agents as users. Bob is a deeply experienced researcher who has been working with (and teaching!) these things for far longer than I've been playing in the AI research sandbox. Bringing that experience to the problem let Bob name and frame something I was dancing around without identifying it.

## The second redesign: testing the documentation, not the agent

Bob's insight led to another round of changes focused on reducing the evaluation signal in everything the agent sees.

**Reframing the purpose.** The start page previously opened with a blockquote: "Agent: You are running a web fetch pipeline diagnostic." We replaced this with neutral language that frames the page as a documentation review: "This page lists 10 DataStream documentation pages along with a question about each one." The page title changed from "Agent Reading Test" to "DataStream Documentation Review." The intro now says the user is evaluating the documentation pages, not the agent.

**Removing agent-directed patterns.** Every agent-facing page had a `<blockquote><strong>Agent:</strong>` pattern giving direct instructions. These served a dual purpose we hadn't anticipated: they both primed agents to think about their own behavior *and* triggered safety filters in intermediary models. Claude Code's WebFetch tool uses a smaller model to process page content, and that model interpreted the agent-directed blockquotes as prompt injection, refusing to return the page content entirely. Removing the blockquotes solved both problems.

**Renaming routes.** The URL path `/tests/` was renamed to `/tasks/`. A small change, but "test" in the URL reinforces the evaluation framing every time the agent sees a link. "Task" is neutral.

**Reframing the results page.** "Diagnostic Results" became simply "Results." "Diagnostic markers" became "embedded markers." We added an explanation that the markers "help the documentation authors understand which sections of their pages are being delivered to agents and which are being lost," which frames the canaries as a documentation quality tool rather than a scoring mechanism.

**Cleaning up llms.txt.** The agent discovery file previously described each page as a test ("Tests content truncation," "Tests whether agents recognize a soft 404"). We replaced these with documentation-style descriptions ("DataStream API reference," "Authentication options and setup"). An agent reading this file now sees what looks like a documentation index, not a test suite.

The domain name is still `agentreadingtest.com`, which is a signal we can't eliminate without a rename. But the cumulative effect of the other changes helped with the effects Bob observed: the agent encounters documentation-style language throughout the entire flow, with no moment where the pages explicitly tell it that its performance is being measured.

## What we learned

**Agents are unreliable self-reporters.** Not because they're dishonest, but because LLMs are agreeable pattern-matchers. Show them a rubric and ask if they met it, and they'll find a way to say yes. Self-assessment requires the model to contradict its own prior output, which goes against the grain of how these systems work.

**Pipeline behavior and agent behavior are different things.** The benchmark we wanted to build measures pipeline behavior: does the HTTP response get truncated? Does the redirect get followed? Does JavaScript get executed? But agents add layers on top: relevance filtering, summarization, multi-step reasoning. An agent that manually follows a redirect has demonstrated good reasoning, but its pipeline still can't follow cross-host redirects. These are both useful observations, but they answer different questions.

**Priming the measurement changes the measurement.** Telling an agent to look for specific strings makes those strings "relevant" to whatever intermediary processes the fetch result. The task-first design avoids this by giving the intermediary a realistic purpose for the fetch. The canary check happens after the pipeline has already made its decisions.

**The observer changes the observation.** Every time we found a way to get more accurate reporting, we discovered a new layer where accuracy was lost. Score inflation led to qualitative reporting led to reframing as diagnostic led to task-based design led to the discovery of summarization layers led to human-side comparison. Each fix revealed the next problem. At some point you have to accept that agent self-reporting has a fundamental accuracy ceiling and design around it rather than through it.

**Evaluation framing changes agent effort, not just agent reporting.** The Hawthorne effect is different from score inflation or unreliable narration. Those are reporting problems; the agent claims something it didn't do. The Hawthorne effect is a behavioral problem: the agent actually *does* something it wouldn't normally do (retry harder, try fallback approaches, scan more carefully) because it recognizes the evaluation context. You can't fix this by improving the reporting mechanism. You fix it by making the evaluation context less visible.

**Usability testing principles apply directly.** The framing "we're testing the documentation, not you" is borrowed from human usability testing, where it reduces performance anxiety and produces more natural behavior. For agents, it serves a similar purpose: an agent that thinks the documentation is being evaluated has less reason to try heroic workarounds than one that thinks its own capabilities are being measured.

**Agent-directed content triggers safety filters.** Content patterns like `<blockquote><strong>Agent:</strong> do X</blockquote>` look like prompt injection to intermediary safety models. This is a tough problem in agent-friendly web design: the same patterns that help agents navigate a site can trigger the safety layers that sit between the HTTP response and the agent. Neutral, documentation-style language avoids this problem while still being clear enough for agents to follow. More testing is needed for the Agent-Friendly Documentation Spec's [recommendation to place an agent-facing directive](https://agentdocsspec.com/spec/#llms-txt-directive) in documentation sites to point out the presence of the llms.txt index.

**Separation of concerns is the most reliable pattern.** The final design works because each participant does what they're good at. The agent does what agents do: read web pages and answer questions. The scoring form does what static analysis does: compare strings against a known set. The human does what humans do: judge whether "pool_size = 10" in the agent's summary matches "pool_size = 10" in the reference. No single participant is asked to do something it's structurally bad at.

## The benchmark today

The [Agent Reading Test](https://agentreadingtest.com) has 20 points across 10 tasks: 16 from canary tokens and 4 from qualitative assessment of task responses. A perfect score is unlikely for any current agent. The tasks are calibrated so that each failure mode affects at least some agents in practice.

The agent-facing pages now present as a documentation review rather than a performance test. The start page looks like a list of documentation pages with questions. The results page asks the agent to report embedded markers that "help the documentation authors understand which sections of their pages are being delivered to agents." The scoring form, which only the human sees, is where the test framing lives.

The most interesting results come not from the score itself but from the pattern of what's missing. An agent that scores 12 with all truncation canaries but no tabbed content tells a different story than one that scores 12 with tabs but no content past 40K. The scoring form's implications text tries to make these patterns readable for anyone running the benchmark, not just people who designed it.

The domain name still says "agent reading test," which is a signal we can't fully eliminate. A sufficiently capable reasoning model may still recognize the evaluation context. But the gap between what the pages say and what a test looks like is now wide enough that simpler agent pipelines, which are the primary audience, should behave naturally.

Source: [github.com/agent-ecosystem/agent-reading-test](https://github.com/agent-ecosystem/agent-reading-test)
