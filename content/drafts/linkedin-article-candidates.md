---
title: LinkedIn Article Candidates
author: Dachary Carey
layout: post
description: Working list of LinkedIn notes that could grow into full articles, with the source notes linked.
date: 2026-10-18 16:00:00 -0400
tags: [ai, agents]
draft: true
---

This is a planning doc, not a post. Each candidate below links to the microblog post(s) it would grow out of, in the [microblog](https://dacharycarey.com/microblog/), and to the LinkedIn originals. Posts marked *(review)* name people and are still `draft: true` pending a check.

Roughly in priority order.

## 1. Version information is invisible to agents

**Thesis:** Humans see version context in the page chrome; agents reading markdown don't. Reproducible failures on two axes (installing stale versions, reaching for functionality the installed version lacks), plus a small-model summarizer inventing a plausible `stop` command. Open question: should the product add the command the model expects?

- [The more I dig into how agents fail on tasks](https://dacharycarey.com/microblog/2026/10/01/the-more-i-dig-into-how-agents-fail-on-tasks-the-more-i/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7511405397119799297))

Related existing articles: [Agent Web Fetch Spelunking](https://dacharycarey.com/2026/02/19/agent-web-fetch-spelunking/), [Is Your llms.txt Already Stale?](https://dacharycarey.com/2026/03/15/is-your-llms-txt-already-stale/).

## 2. Technical writing is where implementation validates design

**Thesis:** AI-drafted process docs tracked neither the design doc nor the codebase. The reconciliation work (what was decided, why, and what users need to know) lives in heads and conversations nobody wrote down. No context platform captures it.

- [Technical writing is where implementation validates design](https://dacharycarey.com/microblog/2026/06/02/technical-writing-is-where-implementation-validates-design/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7467548094780563456))

Related: [Confident-Sounding Gibberish](https://dacharycarey.com/2026/03/30/confident-sounding-gibberish/), [The Verification Gap in AI Content Pipelines](https://dacharycarey.com/2026/03/29/ai-content-pipelines-verification-gap/).

## 3. Agents work around your eval constraints

**Thesis:** Agents don't use products the way the team designing them expects. They wrote their own scripts instead of using the provided tool, and one harness imported urllib to route around a disabled web fetch tool. If you design "no docs" conditions, check how agents escaped them.

- [Week 4: agents don't use your product like humans do](https://dacharycarey.com/microblog/2026/08/14/at-the-end-of-week-4-of-new-job-heres-what-im-thinking-about/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7493997332783419392))
- [Codex visited one page 7 times](https://dacharycarey.com/microblog/2026/08/20/another-random-agents-are-using-your-stuff-in-weird-ways/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7496218489100152832))
- [One run is not enough](https://dacharycarey.com/microblog/2026/08/25/fyi-one-run-is-not-enough-to-prove-agents-can-or-cant-do/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7497996728520880128))
- [FastAPI is the default vibe-coded server](https://dacharycarey.com/microblog/2026/07/30/random-observation-fastapi-has-become-the-default-vibe/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7488621371141419008))

## 4. Lint your prose, because style guides are suggestions

**Thesis:** Markdown style guides in skills or AGENTS.md are advice an agent can ignore. A Vale pre-commit hook with AI-writing rules is enforcement. This repo already has `check_prose_style` and `.vale/styles/DC`, so the article can show the actual rules.

- [LLM writing is just not very good](https://dacharycarey.com/microblog/2026/08/01/ive-been-chatting-with-a-few-folks-lately-about-how-llm/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7489522863750909952))
- TFW you trigger your own Vale rules (excluded from the microblog as an article announcement; [LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7508328494318407681))

Related: [Drafting Editorial Content with AI](https://dacharycarey.com/2026/03/26/drafting-editorial-content-with-ai/).

## 5. Where eval prompts come from

**Thesis:** Engineers assume tests and evals are the same skill; they aren't. Realistic prompts come from existing DX artifacts (personas, jobs-to-be-done, journeys) and from public GitHub PRs that integrated your product, with a cheating risk when agents can look up the finished work. What an MVP eval set contains, and evals as part of definition of done.

- [Week 2: evals](https://dacharycarey.com/microblog/2026/07/31/at-the-end-of-week-2-of-new-job-heres-what-im-thinking/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7488941672676896768))
- [Week 3: eval design](https://dacharycarey.com/microblog/2026/08/07/at-the-end-of-week-3-of-new-job-heres-what-im-thinking/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7491475080120623104))
- [Week 5: what does MVP look like](https://dacharycarey.com/microblog/2026/08/21/week-5-at-new-job-and-heres-what-im-thinking-about/) *(review)* ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7496536464487510016))
- [Realistic user prompts are hard](https://dacharycarey.com/microblog/2026/09/30/a-thing-i-have-been-seeing-is-that-its-really-hard-for/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7511040405828800512))

## 6. Agents learn your product from more than your docs

**Thesis:** Case studies, marketing pages, blog posts, and third-party coverage all shape an agent's model of a product, and they conflict. Positioning changes lag in agents for months. Terminology evals haven't yet found a case where vocabulary changes outcomes, which is itself a finding.

- [Week 8: product positioning changes](https://dacharycarey.com/microblog/2026/09/11/week-8-at-new-job-and-heres-what-im-thinking-about/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7504161547733217282))
- [Week 9: content surfaces beyond docs](https://dacharycarey.com/microblog/2026/09/18/week-9-at-new-job-and-heres-what-im-thinking-about/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7506698494477426688))
- [Week 10: vocabulary and agent outcomes](https://dacharycarey.com/microblog/2026/09/25/week-10-at-new-job-and-heres-what-im-thinking-about/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7509238403083976704))

## 7. Fix your errors before you write skills

**Thesis:** Every customer hits errors; not every customer installs your skills. Agents churn and fail to recover when error information is thin, so error remediation is the most universal agent experience improvement. Would be stronger with recovery-path data from agentminutes.

- [Error information is a force multiplier](https://dacharycarey.com/microblog/2026/09/17/this-is-a-great-example-of-a-high-impact-force-multiplier/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7506431786302988288))
- [Crowdsourcing prior art on agent failure recovery](https://dacharycarey.com/microblog/2026/05/12/ok-linkedin-network-help-me-crowdsource-from-your-lovely/) *(review)* ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7459953476874231808))

## 8. Four character units, one spec field

**Thesis:** "Characters" in the Agent Skills spec is ambiguous. Harnesses count description length in at least four different units, and one rejects non-ASCII names outright. A small, concrete post about why under-specified specs break portability.

- [Another busy weekend for my open-source projects](https://dacharycarey.com/microblog/2026/09/21/another-busy-weekend-for-my-open-source-projects/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7507774303522873344))
- [Copilot CLI in the implementation benchmark](https://dacharycarey.com/microblog/2026/09/26/what-happens-when-you-run-another-coding-agent-through-a/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7509696521479725057))
- [Your Agent Skill isn't the same on every platform](https://dacharycarey.com/microblog/2026/08/03/your-agent-skill-isnt-the-same-on-every-platform/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7490017153882959874))

Related: [When a Feature Request Becomes a Research Project](https://dacharycarey.com/2026/03/21/when-feature-request-becomes-research-project/).

## 9. Twelve ways to write a procedure

**Thesis:** Incremental adoption of docs platform features leaves old content on old patterns. Three procedure syntaxes times two mutually-exclusive-path syntaxes times two composition orders is twelve variants any tooling (or any agent) has to understand. A kind of docs tech debt nobody talks about.

- [Programmatically testing procedures](https://dacharycarey.com/microblog/2025/12/03/i-recently-spiked-on-what-it-would-take-to-programmatically/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7402003329880440832))

## 10. Don't LLM-copy your docs into skills

**Thesis:** Pointing an LLM at docs and saying "make this a skill" creates a probabilistic second source of truth that drifts and regenerates differently every time. Generate skills deterministically from docs source instead.

- [Agent Skills are the new hot topic](https://dacharycarey.com/microblog/2026/02/12/agent-skills-are-the-new-hot-topic-as-ai-assisted/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7427864632553558016))
- [Modular content for just-in-time context](https://dacharycarey.com/microblog/2026/02/02/heya-technicalwriting-hive-mind-i-am-curious-for-your/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7424194427092627456))

Related: [Case Study - 'upgrade-stripe' Agent Skill](https://dacharycarey.com/2026/02/27/case-study-upgrade-stripe-skill/).

## 11. Small tools, big leverage

**Thesis:** Technical writers who build CLIs and learn their version control APIs get force multipliers: make it a CLI not a script, watch the code not Jira, keep a terminal utility library.

- [Learn your version control APIs](https://dacharycarey.com/microblog/2025/08/01/if-you-are-a-technical-writer-who-documents-software/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7357016214532964353))
- [Make it a CLI instead](https://dacharycarey.com/microblog/2025/10/17/i-set-out-to-write-a-script-today-to-figure-out-how-many/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7385078547272450048))
- [Little utilities that meet you where you are](https://dacharycarey.com/microblog/2026/02/02/one-of-the-writers-in-the-org-just-reported-an-issue-with/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7424208970422063105))

## 12. The local inference weekend

**Thesis:** Pi is an agent Emacs with no batteries included. Local models on a single consumer GPU are blazing fast and a year or two behind the frontier; getting them running took seven hours of dependency hell. Promised write-up that never happened.

- [Weekend project: digging into Pi](https://dacharycarey.com/microblog/2026/06/06/weekend-project-im-finally-digging-into-pi/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7469010844606078976))
- [Local AI was not a home run](https://dacharycarey.com/microblog/2026/06/08/whomp-whomp-weekend-poking-of-local-ai-was-not-a-home-run/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7469729160677068800))
- [A gaming PC is not an inference workstation](https://dacharycarey.com/microblog/2026/08/26/not-gonna-lie-i-didnt-make-a-lot-of-headway-with-my-local/) ([LinkedIn](https://www.linkedin.com/feed/update/urn:li:share:7498358773217050624))

## Maybe

- **Maturing an official skills program.** Gating before authoring, maintenance manifests with review triggers, skills-as-a-service, and the six-dimension review guidance. Partly covered by [A Skill is More than Markdown](https://dacharycarey.com/2026/05/11/skill-more-than-markdown/). Sources: [maturing the process](https://dacharycarey.com/microblog/2026/05/06/now-that-weve-shipped-our-official-company-agent-skills-mvp/), [questions for distributors](https://dacharycarey.com/microblog/2026/04/28/in-agent-skills-land-questions-for-folks-who-are/), [skills-as-a-service](https://dacharycarey.com/microblog/2026/04/14/ive-had-an-idea-and-am-wondering-if-anybody-is-doing-this/), [review guidance](https://dacharycarey.com/microblog/2026/04/20/in-agent-skill-land-i-have-been-synthesizing-my-100-review/). The review guidance currently links to MongoDB meta docs, which may rot; a site copy would be worth it on its own.
- **Progressive disclosure fails when agents don't ask.** Skills, MCP tool discovery, and docs pages all depend on the agent deciding it needs more; often it guesses instead. [Week 6](https://dacharycarey.com/microblog/2026/08/28/week-6-at-new-job-and-heres-what-im-thinking-about-in-agent/).
- **Vendor lock-in with agent platforms.** Individuals can stay tool-agnostic; orgs accrete CLAUDE.md files and hooks. [Is your org planning for the next platform shift?](https://dacharycarey.com/microblog/2026/04/27/is-your-org-planning-for-the-next-agent-platform-shift-or/)
- **How to read an agent research paper.** Methodology critique of the coding-agents-and-docs paper (83.8% Claude Code sessions, 16 retrieval events). [Interesting research paper dropped](https://dacharycarey.com/microblog/2026/08/23/interesting-research-paper-dropped-on-the-20th-about-how/). Might fit aeshift better.
- **Pillars of code examples.** Coverage, maintenance, discoverability, and the hot take that audience-level code examples never work in practice. [Pillars](https://dacharycarey.com/microblog/2024/09/17/continuing-to-think-about-code-examples-in-documentation/), [hot take](https://dacharycarey.com/microblog/2024/09/12/i-got-a-lot-of-interesting-and-thoughtful-replies-to-my/), [questions](https://dacharycarey.com/microblog/2024/09/11/ive-been-thinking-a-lot-lately-about-code-examples-in/).
- **Google Docs and markdown.** The round-trip workflow for internal docs and the sync tool that doesn't exist. [Drafting internal docs](https://dacharycarey.com/microblog/2025/11/19/ive-been-exploring-how-ai-can-help-me-with-drafting/), [the missing collaboration tool](https://dacharycarey.com/microblog/2026/04/03/instead-of-shoving-ai-into-all-the-software-that-doesnt/).
- **De-clouding the smart home.** Home Assistant, Zigbee, and no listening devices; a non-AI post for the Programming side. [Smart home milestones](https://dacharycarey.com/microblog/2025/11/02/in-personal-project-news-this-weekend-my-household-hit-two/).
- **The handoff is the new friction.** Agents write the app; humans still click through App Store Connect. [Fable built me an app](https://dacharycarey.com/microblog/2026/08/10/fable-built-me-a-new-app-over-the-weekend/), [the next horizon](https://dacharycarey.com/microblog/2026/08/11/thinking-more-about-my-fable-ios-app-development-project/).

## Your call

- **A pragmatic view at an AI company.** The "I haven't seen evidence of that" exchange. Already public, but it names the employer context. [Note](https://dacharycarey.com/microblog/2026/09/08/there-is-a-slightly-weird-thing-about-working-at-an-ai/).
- **The first 90 days series as one article.** Weeks 1 through 12 form an arc from "agents aren't humans" to "how do I scale myself." Some of it touches internal work that your Sep 21 note said goes through a publishing approval process. [Week 1](https://dacharycarey.com/microblog/2026/07/24/ok-at-the-end-of-week-1-of-new-job-heres-what-im-thinking/) through [Week 11](https://dacharycarey.com/microblog/2026/10/02/week-11-at-new-job-and-heres-what-im-thinking-about/).
