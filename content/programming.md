---
title: Programming
permalink: /programming/
image: '/images/tokendiff-fenestro.png'
---

I build tools that solve problems I encounter in my own workflow - whether that's managing pull requests across repositories, getting readable diff output, or ensuring code examples actually work.

---

## Developer Tools & Libraries

Tools I've built to solve developer workflow problems.

### tokendiff

A Go library and CLI for human-readable, token-level diffing.

Standard diff tools optimize for minimal edit distance, but often produce output that's hard for humans to parse - fragmenting changes across common words like "the" or "for." tokendiff uses a histogram-based algorithm with heuristics tuned for readability.

- **Language**: Go
- **State**: Released
- **Links**: [GitHub](https://github.com/dacharyc/tokendiff) ・ [Blog post: Diff Algorithm Spelunking](/2025/12/29/diff-algorithm-spelunking/)

### diffx

A Go implementation of the Myers O(ND) diff algorithm with preprocessing, heuristics, and postprocessing for improved output quality.

Built as the algorithm layer underneath tokendiff, with a clean `Element` interface for custom diffing beyond strings.

- **Language**: Go
- **State**: Released
- **Links**: [GitHub](https://github.com/dacharyc/diffx)

### OASprey

OpenAPI specification validation for Jest. Validates that HTTP responses match their OpenAPI schemas at test time.

Forked and streamlined from OpenAPIValidators to reduce dependencies and improve maintainability.

- **Language**: TypeScript
- **State**: Published on npm
- **Links**: [GitHub](https://github.com/grove-platform/OASprey) ・ [npm](https://www.npmjs.com/package/oasprey)

### fenestro

A Go CLI that ingests HTML and renders it in helpful windows. Built to support diff output visualization.

- **Language**: Go
- **State**: In development
- **Links**: [GitHub](https://github.com/dacharyc/fenestro)

---

## Documentation Infrastructure

Tools built for my work at MongoDB, focused on code example quality at scale.

### Audit CLI

A Go CLI for analyzing documentation source files - tracking code examples, includes, procedures, and cross-references across large documentation sets with monorepo structure awareness.

Used to audit content and infrastructure to perform maintenance work, product updates, or identify needed process improvements and education opportunities.

- **Language**: Go
- **State**: Active development
- **Scope**: 40+ documentation projects, 35,000+ code examples, one zillion documentation source files
- **Links**: [GitHub](https://github.com/grove-platform/audit-cli) ・ [Documentation](https://www.mongodb.com/docs/meta/grove/audit-cli/)

### GDCD (Great Docs Code Devourer)

Fetches and analyzes documentation data from internal APIs, tracking code examples and page metadata. Used to audit 35,000+ code examples across 40+ repositories, resulting in an 88-page analysis for documentation leadership.

- **Language**: Go
- **State**: Maintenance mode (used weekly)

### DODEC (Database of Devoured Code)

Query tool for aggregating and reporting on code example data stored in MongoDB. Supports weekly and monthly reporting and trend analysis.

- **Language**: Go
- **State**: Maintenance mode (used weekly)

### Cross-Language Comparison Frameworks

Testing utilities for validating code example output across programming languages. Each implementation provides a fluent API for comparing actual output against expected results, with support for MongoDB-specific types, ellipsis patterns, and flexible matching.

- **Languages**: Go, Java, Python, C#/.NET, JavaScript, MongoDB Shell
- **State**: Active development
- **Scope**: Used across MongoDB's documentation

---

## macOS Apps

Native macOS applications built with SwiftUI.

### PR Focus

A native macOS dashboard app for managing pull request activity across GitHub repositories. Helps developers track incoming PRs, review requests, and PR status without context-switching to the browser or having to check many repositories.

- **Stack**: SwiftUI, Realm Database, GitHub GraphQL API
- **State**: [Live on Mac App Store](https://apps.apple.com/us/app/prfocus/id6449602269) ・ [Website](https://prfocus.app)

### Issuenator

Watches GitHub repositories for new issues and surfaces them in a native interface. Built for developers who maintain multiple repositories.

- **Stack**: SwiftUI, SwiftData
- **State**:  TestFlight Alpha ・ [Website](https://issuenator.app)

### Geta

A GM toolkit for tabletop gaming - generates shops, taverns, NPCs, and other elements for game masters.

- **Stack**: SwiftUI, SwiftData
- **State**: In development

---

## iOS Apps

Native iOS applications built with SwiftUI.

### Shattered Ring

A companion app for the video game *Elden Ring* - helps players track NPCs, Quests, Locations, and Bosses since the game did not provide an in-game system for this.

- **Stack**: SwiftUI, Realm Database
- **State**: [Live on App Store](https://apps.apple.com/app/shattered-ring/id1632437036) ・ [Website](https://shatteredring.com)

### Pocket Codex

A reference app for video game data - provides searchable, offline access to game information. Includes a custom data pipeline with web crawling, HTML parsing, and automated code generation.

- **Stack**: SwiftUI, SwiftData, Go (data pipeline)
- **State**: TestFlight Alpha

### TeaLixir

A tea-tracking app for tea enthusiasts - save tea details, log preparations with additions and tasting notes, and discover patterns in your preferences.

- **Stack**: SwiftUI, SwiftData, CloudKit
- **State**: Pending Release ・ [Website](https://tealixir.app)

### Coffeelicious

A coffee-and-espresso tracking app for coffee enthusiasts - save bean details, log preparations with additions and tasting notes, and discover patterns in your preferences. (TeaLixir, but for coffee!)

- **Stack**: SwiftUI, SwiftData, CloudKit
- **State**: In development

---

## Writing & Workshops

Technical content about building software.

### Blog

I write about software development, documentation infrastructure, and the tools I build:

- [Diff Algorithm Spelunking](/2025/12/29/diff-algorithm-spelunking/) - Designing human-readable diff output
- [Code Example Audit Series](/2025/03/02/code-example-audit-overview/) - Auditing 35,000+ code examples at scale
- [Testing Documentation Code Examples](/2024/01/12/how-to-test-docs-code-examples/) - Unit testing approaches across 9 SDKs

### Workshops

Training materials for technical writers on code example testing and documentation workflows:

- Creating Tested Code Examples
- Working with Tested Code Examples
- Removing Hard-Coded Code Blocks

---

## Technical Focus

**Primary languages**: Go, Swift, TypeScript

**Domains**: Developer tooling, documentation infrastructure, native app development

**Approach**: I build tools that solve problems I actually have. Everything here started as "I wish this existed" and became "I'll build it myself."
