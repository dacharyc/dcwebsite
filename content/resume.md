---
title: Resume
permalink: /resume/
---

# Dachary Carey

**Developer Experience | Documentation Infrastructure | Ecosystem Analysis**

GitHub: [dacharyc](https://github.com/dacharyc) | LinkedIn: [dachary](https://www.linkedin.com/in/dachary/) | Web: [dacharycarey.com](https://dacharycarey.com)

---

<a href="/dachary-carey-resume.pdf" download class="button button--primary">Download PDF version</a>

## Summary

I build tools, infrastructure, and research that help developers succeed. My work combines nearly two decades of professional writing with software engineering, giving me an unusual ability to move between building systems and communicating about them clearly.

At MongoDB, I design testing frameworks and audit tooling for code example quality across 40+ documentation projects. Independently, I conduct ecosystem-scale research on AI agent tooling, build developer tools in Go and Swift, and ship apps on the Mac App Store and iOS App Store.

The thread connecting all of it: I'm driven to understand how systems actually work, not just how they're supposed to work. That curiosity has taken me from documenting SDKs to building cross-language testing infrastructure, from writing diff algorithms to analyzing hundreds of AI agent skills for structural and content quality.

For more detail, see [Documentation & Developer Education](/documentation/) and [Programming](/programming/).

---

## Experience

### Senior Programmer Writer — MongoDB (2021 - Present)

Developer Education team. Started as the first writer on a team of developers; evolved from SDK documentation into infrastructure and tooling work.

**Documentation Infrastructure & Tooling**

- Designed Grove, a cross-language code example testing framework supporting 6 languages (Go, Java, Python, C#, JavaScript, MongoDB Shell) across 40+ documentation projects
- Built Audit CLI (Go): 15+ commands for documentation analysis, including code extraction, include dependency trees, cross-version comparison, and page/example metrics
- Created comparison libraries with configurable matching (ordered/unordered arrays, field value ignoring for dynamic data like ObjectIds and timestamps, MongoDB type support)
- Built sample data utilities that automatically skip tests when datasets aren't available, reducing contributor friction while CI runs the full suite
- Led audit of 35,000+ code examples across 40+ repositories, producing an 88-page analysis with recommendations for documentation leadership
- Created OASprey (TypeScript): OpenAPI validation library published to npm for verifying API response schemas at test time
- Designed and delivered three workshops on code example testing for technical writers

**SDK Documentation (earlier focus)**

- Owned documentation and tested code examples for Swift SDK, SwiftUI, C++ SDK; contributed to Kotlin, TypeScript, JavaScript, Flutter/Dart
- Led information architecture overhaul from Diataxis framework to task-based structure, informed by user research
- Led Docs Maintenance Working Group; created processes now part of content strategy
- Built GitHub workflow for automated readability scoring on pull requests

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

### Agent Skill Ecosystem Analysis (2026)

Conducted a systematic quality and content analysis of the Agent Skills ecosystem, evaluating 673 skills from 41 source repositories. Published findings as a research paper and interactive report.

- Built skill-validator (Go): validates Agent Skills against the agentskills.io specification, checking structure, frontmatter, content quality, cross-contamination risk, and token budget composition
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

### Developer Tools (Go)

- **tokendiff**: Library and CLI for human-readable, token-level diffing using a histogram-based algorithm tuned for readability
- **diffx**: Myers O(ND) diff algorithm implementation with a clean Element interface for custom diffing beyond strings
- **fenestro**: CLI that ingests HTML and renders it in helpful windows for diff output visualization
- **skill-validator**: Validates Agent Skills against the agentskills.io specification

### Apps (Swift/SwiftUI)

- **PR Focus**: macOS dashboard for managing pull request activity across GitHub repositories. Live on Mac App Store.
- **Shattered Ring**: iOS companion app for Elden Ring. Live on App Store.
- **TeaLixir**: iOS tea-tracking app with tasting notes and preference patterns. Live on App Store.
- **Issuenator**: macOS app for watching GitHub repositories for new issues. TestFlight alpha.
- **Pocket Codex**: iOS reference app with custom data pipeline (Go web crawling, HTML parsing, automated code generation). TestFlight alpha.

---

## Skills

**Languages**: Go, Swift (primary); TypeScript, Python, JavaScript, C# (professional use building cross-language testing infrastructure)

**Domains**: Developer tooling, documentation infrastructure, ecosystem analysis, native app development, technical communication

**Approach**: I build tools that solve problems I encounter firsthand, investigate systems to understand how they actually work, and communicate findings clearly. My career has been shaped by the intersection of writing and engineering; I bring both to every project.

---

## Education

Self-taught developer. Professional writer since 2007, software development since 2016. Technical skills built through a decade of production work across documentation infrastructure, developer tooling, and independent software projects.
