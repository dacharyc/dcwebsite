---
title: Resume
permalink: /resume/
---

# Dachary Carey

**Agent Experience | Developer Tooling | AI Agent Research**

GitHub: [dacharyc](https://github.com/dacharyc) | LinkedIn: [dachary](https://www.linkedin.com/in/dachary/) | Web: [dacharycarey.com](https://dacharycarey.com)

---

<a href="/dachary-carey-resume.pdf" download class="button button--primary">Download PDF version</a>

## Summary

I build tools, infrastructure, and research that help developers succeed. My work combines nearly two decades of professional writing with software engineering, giving me an unusual ability to move between building systems and communicating about them clearly.

At NVIDIA, I define agent experience (AX) standards that make developer surfaces work for both human developers and the AI agents they bring with them. Before that, at MongoDB, I owned Agent Skill quality standards and designed the testing frameworks and audit tooling behind code example quality across 40+ documentation projects. Independently, I research how AI agents consume web content and documentation, author specifications adopted by commercial platforms, build developer tools in Go and TypeScript, and ship apps on the Mac App Store and iOS App Store.

The thread connecting all of it: I'm driven to understand how systems actually work, not just how they're supposed to work. That curiosity has taken me from documenting SDKs to building cross-language testing infrastructure, from writing diff algorithms to designing benchmarks that reveal how agent fetch pipelines fail.

For more detail, see [AI & Agent Research](/ai-research/), [Documentation & Developer Education](/documentation/), and [Programming](/programming/).

---

## Experience

### Developer Experience Manager, Agents — NVIDIA (2026 - Present)

I define agent experience (AX) standards across NVIDIA's developer ecosystem, making developer surfaces work for both human developers and the AI agents they bring with them.

- Building integrated developer journeys across NIM, NGC, NeMo, AI Blueprints, and CUDA surfaces for developers and their coding agents
- Defining practical AX standards: content sizing and budgeting, information architecture, discoverability (llms.txt, content negotiation), onboarding patterns
- Guiding teams on agent-facing tooling: MCP servers, Agent Skills, API documentation patterns, agent-consumable tests
- Evaluating content performance using human engagement metrics and agent traffic signals

### Senior Programmer Writer — MongoDB (2021 - 2026)

Developer Education team. Started as the first writer on a team of developers; grew from SDK documentation into code quality infrastructure and AI builder experience across 5.5 years.

**AI Builder Experience (2026)**

- Quality and launch-readiness owner for MongoDB's first official Agent Skills: built skill-gate (security scanning for skill pull requests), enterprise validation tooling, and authoring and review guidance
- Authored the Agent-Friendly Docs product definition and open-model/open-harness research briefs that informed AI strategy
- Co-built an AI news aggregation and tagging pipeline (Skunkworks 2026), retained as a permanent internal AI-strategy resource

**Code Quality Infrastructure (2024 - 2026)**

- Designed Grove, a cross-language code example testing framework spanning 6 languages (Go, Java, Python, C#, JavaScript, MongoDB Shell) across 40+ documentation projects, with comparison libraries and sample data utilities that let technical writers validate examples without learning developer test frameworks
- Led the first programmatic audit of MongoDB's 35,000+ code examples; findings became the basis for the org's FY27 code examples strategy
- Built Audit CLI (Go, 15+ commands for documentation analysis) and modernized OASprey (TypeScript OpenAPI validator on npm, used to verify the Cloud Status API docs before publication)
- Designed and delivered three workshops on code example testing for technical writers

**SDK Documentation (2021 - 2025)**

- #1 contributor to both the Realm/Device SDK and App Services docs repos over 4+ years; stood up the entire C++ SDK documentation from scratch and was primary author for the Swift SDK
- Pioneered topic-based information architecture on the Swift SDK and drove its rollout across the SDK docs
- Built automated readability scoring for pull requests; mentored writers and interns into developer-writer roles

### Technical Content Strategist — Tugboat (2019 - 2021)

Led documentation infrastructure and content strategy for a developer-focused Docker-based preview environment platform.

- Migrated legacy GitBook documentation to a modern Hugo-based docs-as-code portal
- Completed information architecture overhaul to task-oriented developer documentation
- Created starter configurations for popular frameworks (Hugo, Jekyll, MkDocs, Rails)
- Implemented analytics pipeline connecting documentation usage to product and marketing insights

### Contract Technical Writer — One Door (2016 - 2018)

First dedicated technical writing role. Built documentation infrastructure for a retail SaaS platform.

- Created online help portal from legacy Word documentation
- Developed persona-based documentation architecture for different user types
- Wrote API documentation for both technical and business audiences
- Served as internal subject matter expert for application functionality

### Technical Writer and Content Strategist — Contract (2007 - 2016)

Over a decade of contract writing across diverse industries and technology stacks. Each engagement required rapidly immersing in an unfamiliar domain, identifying what mattered, and producing clear, authoritative content under deadline pressure.

From 2016: focused on developer documentation, API docs, and technical content strategy for software companies. Earlier work included business ghostwriting, content marketing, and technical content across healthcare, finance, and manufacturing.

---

## Independent Research

### Agent-Friendly Documentation Spec (2026)

Authored a specification defining 23 checks across 7 categories for evaluating how well documentation sites serve AI agent consumers. Covers llms.txt discovery, markdown availability, page size, content structure, URL stability, and more. Built the companion afdocs CLI (TypeScript, published on npm) that scores any docs site against the spec. Co-built into Fern's public [Agent Score](https://buildwithfern.com/agent-score) directory; CEOs of OpenRouter and Resend endorsed the category at launch. ~9k npm downloads in trailing 14 days (3.5x growth over prior 8 weeks).

### Agent Reading Test (2026)

Designed a benchmark for measuring how AI agent web fetch pipelines handle real-world documentation failure modes. 10 test pages target specific failures (truncation, SPA shells, tabbed content, redirects, soft 404s) using embedded canary tokens. Iterative design addressed score inflation, relevance-layer priming, agent self-assessment bias, and the Hawthorne effect in reasoning models. Published at agentreadingtest.com; hit Hacker News front page (~100k views in 24 hours); Better Stack and GitBook DevRel produced follow-on content; listed in former Django president Thibaud Colas's curated resources.

### Agent Skill Implementation Research (2026)

Empirical research into how agent platforms actually implement skill loading, management, and presentation. Catalogs 23 checks across 9 categories with 17 benchmark skills containing canary phrases for testing platform behavior. Community-driven project accepting per-platform contributions. Published at agentskillimplementation.com.

### Agent Skill Ecosystem Analysis (2026)

Conducted a systematic quality and content analysis of the Agent Skills ecosystem, evaluating 673 skills from 41 source repositories. Published findings as a research paper and interactive report.

- Built skill-validator (Go): validates Agent Skills against the agentskills.io specification, checking structure, frontmatter, content quality, cross-contamination risk, and token budget composition. 25+ public CI adopters including Microsoft (multiple dotnet repos), GitHub, MongoDB, Autodesk, Netlify
- Found that 22% of skills fail structural validation; company-published skills (79.2% pass rate) perform worse than community collections (94.0%)
- Identified that 52% of all tokens across the ecosystem are nonstandard files wasting context window space
- Discovered six content-specific interference mechanisms through behavioral evaluation of 19 representative skills
- Designed and executed LLM-as-judge scoring across all 673 skills, revealing a two-factor quality structure
- Published interactive dashboard at agentskillreport.com and accompanying paper

### Agent Documentation Access Patterns (2026)

Systematically validated 578 coding patterns across 20 skills, documenting how AI agents actually consume documentation in real workflows.

- Cataloged agent URL resolution patterns, failure modes, and practical workarounds
- Identified that agents retrieve docs URLs from training data rather than searching, with implications for content discoverability
- Documented the impact of page length, content serialization, and rate limiting on agent docs access
- Published findings and practical recommendations for documentation teams

---

## Independent Software

### Developer Tools

- **afdocs** (TypeScript): CLI that scores documentation sites against the Agent-Friendly Documentation Spec. Published on npm; powers Fern's Agent Score directory.
- **tokendiff** (Go): Library and CLI for human-readable, token-level diffing using a histogram-based algorithm tuned for readability
- **diffx** (Go): Myers O(ND) diff algorithm implementation with a clean Element interface for custom diffing beyond strings
- **fenestro** (Go): CLI that ingests HTML and renders it in helpful windows for diff output visualization
- **skill-validator** (Go): Validates Agent Skills against the agentskills.io specification

### Apps (Swift/SwiftUI)

- **PR Focus**: macOS dashboard for managing pull request activity across GitHub repositories. Live on Mac App Store.
- **Shattered Ring**: iOS companion app for Elden Ring. Live on App Store.
- **TeaLixir**: iOS tea-tracking app with tasting notes and preference patterns. Live on App Store.
- **Issuenator**: macOS app for watching GitHub repositories for new issues. TestFlight alpha.
- **Pocket Codex**: iOS reference app with custom data pipeline (Go web crawling, HTML parsing, automated code generation). TestFlight alpha.

---

## Talks & Appearances

Featured in the State of Docs Report 2026 discussing AI consumption of documentation. Interview and podcast appearances on agent documentation access, the Agent-Friendly Documentation Spec, and agent reading behavior. See [AI & Agent Research](/ai-research/) for the full list.

---

## Skills

**Languages**: Go, Swift (primary); TypeScript, Python, JavaScript, C# (professional use building cross-language testing infrastructure)

**Domains**: Developer tooling, documentation infrastructure, ecosystem analysis, native app development, technical communication

**Approach**: I build tools that solve problems I encounter firsthand, investigate systems to understand how they actually work, and communicate findings clearly. My career has been shaped by the intersection of writing and engineering; I bring both to every project.

---

## Education

Self-taught developer. Professional writer since 2007, software development since 2016. Technical skills built through a decade of production work across documentation infrastructure, developer tooling, and independent software projects.
