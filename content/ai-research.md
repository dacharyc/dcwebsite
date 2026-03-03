---
title: AI & Agent Research
permalink: /ai-research/
image: '/images/ai-research.jpg'
---

I research how AI agents work, how they consume information, and how the ecosystems around them are evolving. This page collects that work in one place. For my documentation background, see [Documentation & Developer Education](/documentation/). For my programming projects, see [Programming](/programming/).

---

## Talks & Interviews

- **[Why AI Agents Struggle with Modern Documentation](https://youtu.be/T2rXZjtmhRI)** (YouTube, 2026) - Interview covering how agents access documentation in real time and the failure modes most docs teams don't know about.

---

## Specifications & Standards

### Agent-Friendly Documentation Spec

A specification defining 21 checks across 8 categories for evaluating how well a documentation site serves AI agent consumers. Covers llms.txt discovery, markdown availability, page size, content structure, URL stability, and more. Based on real-world agent access patterns I've been researching since late 2025.

- **Links**: [agentdocsspec.com](https://agentdocsspec.com)

---

## Tools

### afdocs

A CLI tool that implements the Agent-Friendly Documentation Spec and tests docs sites against it. Point it at a URL and it reports where your docs stand. Published on npm.

- **Language**: TypeScript
- **Links**: [GitHub](https://github.com/agent-ecosystem/afdocs) ・ [npm](https://www.npmjs.com/package/afdocs)

### skill-validator

A CLI that validates Agent Skills against the agentskills.io specification. Checks directory structure, frontmatter, content quality, cross-contamination risk, and token budget composition.

- **Language**: Go
- **Links**: [GitHub](https://github.com/dacharyc/skill-validator)

---

## Research & Analysis

### Agent Skill Ecosystem Analysis

An ecosystem-scale analysis of 673 Agent Skills across 41 repositories, examining compliance with the Agent Skills specification and content quality. Includes an interactive dashboard and a downloadable paper.

- **Links**: [Interactive Report](https://agentskillreport.com) ・ [Blog post](/2026/02/13/agent-skill-analysis/)

### Agent Web Fetch Behavior

Research into how coding agents actually fetch and process web content, including truncation behavior, redirect handling, and content negotiation across platforms.

- **Links**: [Blog post](/2026/02/19/agent-web-fetch-spelunking/)

### Agent-Friendly Documentation Audit

An analysis of hundreds of documentation pages across popular developer tools, examining how well they serve AI agent consumers. The research that led to the Agent-Friendly Documentation Spec.

- **Links**: [Blog post](/2026/02/18/agent-friendly-docs/)

---

## Writing

I write about agents, documentation, and the AI ecosystem on this blog and at [AE Shift](https://aeshift.com).

**Selected articles:**

- [An Agent is More Than Its Brain](/2026/03/02/an-agent-is-more-than-its-brain/) - What's inside a coding agent, and why the model is only one piece
- [LLMs vs. Agents as Docs Consumers](/2026/02/26/llms-vs-agents-as-docs-consumers/) - Why "AI-friendly docs" means two different things
- [Case Study: upgrade-stripe Agent Skill](/2026/02/27/case-study-upgrade-stripe-skill/) - Deep dive on a real-world Agent Skill
- [Make Your Hugo Site Agent-Friendly](/2026/03/01/make-hugo-site-agent-friendly/) - Practical how-to for static site owners
- [Upskilling in the AI Age](/2026/02/23/upskilling-in-ai-age/) - Advice for people getting started with AI tools

For all AI-related posts, see the [ai tag](/tags/ai/).
