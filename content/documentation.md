---
title: Documentation & Developer Education
permalink: /documentation/
image: '/images/audit-cli-analyze.png'
---

I've spent nine years building documentation systems, testing infrastructure, and creating developer education content. This page covers that work - for my programming projects, see [Programming](/programming/).

---

## MongoDB (2021 - Present)

I joined MongoDB's Developer Education team as the first writer on a team of developers. What started as SDK documentation evolved into building infrastructure for code example quality at scale.

### Current Focus: Documentation Infrastructure

I design and build tooling for code example quality across MongoDB's developer documentation:

- **[Audit CLI](https://github.com/grove-platform/audit-cli)** (Go): Analyzes code examples, includes, and cross-references across 40+ documentation projects, many with their own versions, with monorepo structure awareness. Used to audit content and infrastructure to perform maintenance work, product updates, or identify needed process improvements and education opportunities.

- **GDCD & DODEC** (Go): Tools for fetching, storing, and querying documentation metadata. Used to audit 35,000+ code examples across 40+ repositories, resulting in an 88-page analysis for documentation leadership, and provide ongoing weekly and monthly metrics.

- **Automated pipelines**: CI/CD infrastructure for code example validation and regression testing.

- **[OpenAPI Spec Testing](https://www.mongodb.com/docs/meta/grove/openapi/)** (TypeScript/Jest): Forked and modernized an OpenAPI testing tool. Validates that HTTP responses match their OpenAPI schemas at test time.

#### Grove Code Testing Framework

Designed the testing infrastructure for MongoDB's developer documentation code examples. The framework ensures code examples compile, run, and produce correct output across all supported languages.

**Components I designed:**

- **Comparison libraries** (6 languages): Validate actual vs. expected output with configurable matching
  - Ordered/unordered array comparison
  - Field value ignoring for dynamic data (ObjectIds, timestamps)
  - MongoDB type support (Decimal128, Date, ObjectId)
  - Truncation handling for large outputs

- **Sample data utilities**: Convenience wrappers (`describeWithSampleData`, `itWithSampleData`) that automatically skip tests when MongoDB sample datasets aren't loaded - reduces friction for contributors while CI runs full test suite

- **Process documentation**: Comprehensive guides for technical writers on structuring tests, handling state, working with sample data

**Languages supported:** Go, Java, Python, C#/.NET, JavaScript/Node.js, MongoDB Shell

**Scope:** 40+ documentation projects, 397+ tested code examples

**Education Resources:** Three workshops, an early adopter/UAT working group, and documentation at: [Grove Platform](https://www.mongodb.com/docs/meta/grove/)

### Previous: SDK Documentation

Before the infrastructure focus, I owned documentation and code examples for several Realm SDKs:

**Technical scope:**
- Swift SDK, SwiftUI integration, C++ SDK
- Contributed to Kotlin, TypeScript, JavaScript, Flutter/Dart
- Unit-tested code examples across all platforms

**Process improvements:**
- Led Docs Maintenance Working Group - created processes now part of content strategy
- Built GitHub workflow for automated readability scoring on PRs
- Quarterly readability audits with targeted rewrites

**Information architecture:**
- Overhauled SDK documentation structure based on user research
- Moved from Diátaxis framework to task-based IA
- Regular gap analysis to identify missing content

### Writing Examples

The docs have sadly been removed as the product is deprecated, but the source files are available if you don't mind reading reStructuredText in GitHub:

- [C++ SDK Documentation](https://github.com/mongodb/docs-realm/tree/master/source/sdk/cpp) - wrote from scratch
- [Swift Actor-Isolated Realms](https://github.com/mongodb/docs-realm/blob/86770b2887b693d032b5d2409e371beb5a43f6a1/source/sdk/swift/use-realm-with-actors.txt) - complex, language-specific guide
- [SwiftUI Documentation](https://github.com/mongodb/docs-realm/tree/86770b2887b693d032b5d2409e371beb5a43f6a1/source/sdk/swift/swiftui) - complete section
- Unit-tested code examples: [C++](https://github.com/mongodb/docs-realm/tree/master/examples/cpp) | [Swift](https://github.com/mongodb/docs-realm/tree/master/examples/ios)

---

## Tugboat (2019 - 2020)

Technical content strategy for a developer-focused Docker-based preview environment.

**Infrastructure built:**
- Migrated legacy GitBook documentation to modern Hugo portal
- Docs-as-code workflow with Git-based review and deployment
- Analytics pipeline connecting documentation usage to product insights

**Documentation work:**
- Complete IA overhaul to task-based structure
- Created starter configs for popular frameworks (Hugo, Jekyll, MkDocs, Rails)
- User persona research driving content strategy

**Writing example:** [Tugboat Documentation](https://docs.tugboatqa.com) - created portal, IA, and content, still largely unchanged.

---

## One Door (2016 - 2018)

First dedicated technical writing role. Built documentation infrastructure for a retail SaaS platform.

- Created online help portal system from legacy Word documentation
- Developed persona-based documentation for different user types
- API documentation for technical and business audiences
- Ongoing "detective" work understanding and documenting application functionality

---

## Earlier Work (2007 - 2016)

Freelance writing across industries before pivoting to software documentation:

- Business ghostwriting
- Legal and financial content
- Marketing and content strategy
- Various technical content for software companies

The through-line: understanding complex topics and communicating them clearly. That skill transferred directly to developer documentation.

---

## Approach

Two things have stayed consistent across all this work:

**Build systems, not just content.** Documentation without infrastructure becomes outdated. I focus on processes, automation, and tooling that keep documentation accurate over time.

**Understand the developer experience.** Good documentation meets developers where they are. I test the code, use the products, and build with the same tools our audience uses.
