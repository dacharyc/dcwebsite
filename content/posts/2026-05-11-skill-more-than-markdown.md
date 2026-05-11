---
title: A Skill is More than Markdown
author: Dachary Carey
layout: post
description: In which I work through the actual lifecycle of an official company skill, and find at least six roles that don't exist in most org charts yet.
date: 2026-05-11 07:00:00 -0500
url: /2026/05/11/agent-skill-more-than-markdown/
image: /images/agent-skill-more-than-markdown-hero.png
tags: [ai]
---

Organizations have been pushing official company skills out the door because skills look like just a markdown file, and that seems simple and trivial. But when you dig into the lifecycle of an official skill that you intend to support, distribute, and stand behind, it's a lot more than a markdown file. I'm not sure a single organization shipping skills today recognizes how much more.

This is the article I promised at the end of [Can Agent Skills Make Output Worse?](/2026/05/07/can-agent-skills-make-output-worse/), where I closed by saying I'd been ideating around what skill lifecycle management looks like and would write more about it as I proposed process and tooling around it. This is the lifecycle that I'm picturing. Let me tell you what's actually involved, where the work falls, and where the org chart runs out of people to do it.

## Are skills software, or documentation?

For months, I've been arguing internally that Agent Skills are software, not documentation. The shape of the work (dependencies, evals, versioning, security review, deprecation) looks much more like shipping a software library than like publishing a doc page. I've also seen documentation teams argue the inverse: skills are markdown, markdown is content, content belongs to docs.

I'm now realizing that both sides miss the point.

The real question is organizational: **who is equipped to own each step of the lifecycle?** Once you ask it that way, you find that no single existing function in most companies can own the whole thing. Docs teams own some steps. Engineering owns some steps. Several steps need both, and some need a kind of expertise that doesn't sit in any function in most org charts today.

That reframing is what I want to walk through here, using the lifecycle I've been building out at MongoDB as the concrete example. The shape applies to any organization shipping official skills, not just MongoDB; the named roles and gaps are likely the same for most companies in this position.

Worth noting that here, I'm talking about "official" skills that a company distributes to customers, and intends to support like any other product surface. Your vibe coded personal workflow skill is just fine as it is. I'm not proposing all this ceremony and overhead for personal use skills. But for artifacts that your customers use, this is my ivory tower version of what an ideal end-to-end workflow looks like.

{{< pswp src="/images/diagrams/skill-lifecycle-mongodb.svg" alt="Flow diagram of the proposed MongoDB Agent Skill Lifecycle. The development phase runs from idea or proposal submission through a gating eval (mustInclude / mustNotInclude patterns), triage (JTBD, persona, cross-portfolio scope), skill development, SME review, QA human review, security review, functional evals across LLMs and platforms, routing evals against the existing collection, multi-channel publishing (Cursor, Claude Code, Gemini, Codex, VS Code, docs site), and a public skill catalog update. Continuous maintenance then fans out into regression evals on every model and harness release, spec governance tracking, OWASP advisory monitoring, tool and product version tracking, change governance, user testing, and deprecation policy plus customer comms. Each step is color-coded by the role primarily equipped to own it: pink for docs and writing, blue for software engineering, purple for AI/ML eval engineering, orange for product and program management, green for UX research, and gray for cross-functional or open-intake steps." >}}

## The development phase

Here's the lifecycle I've been sketching for getting a single skill from "someone wants to build this" to "we publish it." Most existing org structures only cover part of this. A docs team's typical lifecycle has analogs for the authoring and review steps but rarely the upstream evals or the cross-model and routing tests. A software team's typical lifecycle has analogs for the build, test, and ship steps but rarely the proposal triage or the instructional-quality review. Neither covers the whole picture.

### 1. Idea / proposal submission

Anyone in the company can submit a skill proposal. An engineer thinks the agent flubs a particular workflow. A PM has heard from customers. A docs writer notices a pattern in support tickets. The submission isn't gated by role; submitters propose the skill they think the company should ship.

What *is* gated is the proposal itself. The submitter has to provide prompts they think the skill should help with, plus must-include and must-not-include patterns describing what good output looks like and what bad output looks like. Those prompts and patterns become the proposal's gating eval.

This step costs almost nothing and is open to everyone.

### 2. Gating eval

We run the submitted prompts through a panel of LLMs across model families *without* the proposed skill in context. If the models reliably produce output matching the must-include patterns and avoiding the must-not-include patterns, we don't need a skill. The model can already do the task. We reject the proposal and move on. But we keep the prompts and patterns in a regression suite, which I'll tell you more about later.

This is where we intentionally introduce the first hurdle. If you want us to ship a skill, you must be able to articulate the specific cases where agents fail with the product today. If you can't articulate the failure mode, you can't write a gating eval that proves the failure mode, and you can't measure whether your skill actually fixes anything. Build a skill anyway and you risk the outcome from my [last article](https://dacharycarey.com/2026/05/07/can-agent-skills-make-output-worse/): a skill that makes the agent's output worse.

Owning this step requires designing eval frameworks: how many models, which families, what statistical thresholds count as "the model already does this," how many prompts are enough. That's an AI/ML eval engineering capability. Not a docs capability, not a general software engineering capability either.

### 3. Triage

If a proposal passes the gating eval, *someone* has to decide whether we'll resource it, what shape it should actually be, and how it fits into the larger collection.

This step doesn't have a clear owner to me yet. It needs:

- **Customer-need understanding** (PM-flavor): is this a real job customers are trying to do, or an artifact of one team's perspective?
- **Cross-portfolio architecture**: how does this skill relate to the rest of the surface? What other skills already cover adjacent territory?
- **Persona awareness**: is this a DBA workflow, a developer workflow, an SRE workflow? They want different things from agents.
- **Job-to-be-done framing**: what's the customer actually trying to accomplish, and is a skill the right shape for that JTBD?
- **Moment-in-time snapshot**: is this something where an agent is failing *right now*, or where it failed last year? Models move fast, and last year's skill may not be needed today.

Let me give you a concrete example of the type of cross-cutting knowledge required. We currently have a PR open to add an Atlas CLI skill. The provided example prompts include things like "how do I upgrade my cluster?" and "how do I scale horizontally to a new region?". Cluster upgrades aren't really an Atlas CLI concern; they should be a dedicated scaling skill that walks an agent through understanding customer needs, estimating pricing, planning around downtime and operational complications, and *then* performing the scaling. Cross-region deployments aren't an Atlas CLI concern either; they should be a skill that understands the nuances of cross-region deployments across cloud providers (AWS, Google Cloud, Azure), because that workflow looks different on each. Scoping all three of those into "the Atlas CLI skill" produces something that does none of them well.

Someone on the triage team needs to be able to look at a proposal and say "this is actually three skills, two of which need to span deployment partners." That kind of cross-portfolio architectural eye doesn't obviously belong to a specific role. Most PMs are focused on their own product surface. Most engineers don't see the whole catalog of skills already in flight. It's not clear to me who in any company today is equipped to do this triage well.

### 4. Skill development

If triage approves a proposal, someone has to actually build the skill. This may not be the person who proposed it. A submitter often has the domain insight but lacks experience writing for LLMs, or vice versa. The triage team has to resource skill development separately, pairing skill-authoring expertise with subject-matter expertise where needed.

Skill development is the step that looks most like documentation, and where docs teams can contribute the most directly. Writing for LLMs is technical writing with quirks: imperative voice matters, structured content matters, examples need to be precisely correct, the description field has real consequences for routing. But it's still recognizably a writing skill.

### 5. SME review

Whoever wrote the skill (whether a writer or an engineer) doesn't necessarily have full domain depth across every product or tool the skill touches. SMEs review for technical accuracy, edge cases, and gaps. This is a long-standing pattern from docs work; it transfers directly.

### 6. QA human review

This is the review I've been arguing the documentation team should own, but with a caveat that's more involved than the usual editorial review.

Part of QA review is conventional: instructional quality, structure, clarity, voice. That's bread and butter for a docs team.

The other part is workflow risk awareness. Some operations are safe in a read-only context and dangerous in a write context. Some commands look harmless in isolation but become destructive when an agent loops over them. Some skills tell agents to perform credential operations that should be wrapped in confirmation prompts. Reviewing for this kind of risk requires understanding both what the skill says to do *and* the operational consequences of doing it. A docs reviewer who has never operated the product can't catch these. An engineer who has never reviewed instructional content for clarity can't catch quality issues that lead to misinterpretation.

I believe QA review at this depth is realistically a docs-plus-engineering pair, or *maybe* a very savvy programmer writer who deeply understands the product portfolio and programming consequences.

### 7. Security review

Beyond workflow risk, there are security-specific concerns: does this skill tell an agent to hand credentials to a third party? Does it perform shell-out operations that could be hijacked through prompt injection? Does it run with permissions higher than necessary? Does it open the door to any of the categories on the [OWASP Agentic Skills Top 10](https://genai.owasp.org/initiatives/agentic-security-initiative/)?

A security reviewer for this work needs to be agent-aware, which is a specialty most security engineers haven't internalized yet. The threat model for an agent context is not the threat model for a web application or for a traditional software library. We're a long way from having broad security-team fluency in this.

### 8. Functional evals

This is the step where we test that the "finished" skill actually produces the expected results, in context, across the LLMs and platforms our customers actually use.

I keep coming back to this open question: should the skill author write the evals, or does someone with eval expertise own them? My current thinking is that authors propose evals as part of submission (at the gating step), but that the production eval suite, the one we're going to run against every new model release for the rest of the skill's life, is owned by an eval-engineering function. Eval design is a specialty. Skill authors aren't always equipped for it, and we shouldn't pretend they are.

Functional evals across model families and across agent harnesses (Claude Code, Cursor, Gemini CLI, Codex, etc.) are infrastructure. You need the API access, the prompt orchestration, the harness adapters, the regression baselines, the statistical reporting. This is recognizably software engineering, with an AI/ML specialty layered on top.

### 9. Routing evals

Once we have a skill that passes its own functional evals, we have to test it against the rest of the collection. Does it trigger when it should? Does it trigger when it shouldn't, stealing the show from a more appropriate skill? Does an existing skill fire on prompts that this new skill is the better answer for? I [wrote about](https://dacharycarey.com/2026/03/13/agent-skill-mega-repo-woes/) what happens when nobody owns this step: 1,200-skill mega repos with 20-skill pile-ups on common topics, where descriptions step on each other, duplicates compete with their own copies, and agents activate by what amounts to coin flip.

For an organization shipping an official collection, routing evals are non-negotiable. Skills are judged collectively, not individually. A user who triggers the wrong skill from your collection blames the company, not the skill. Routing is a capability that doesn't have an obvious home in most engineering orgs because it's not "QA," not "release engineering," not "platform engineering" in the conventional sense. It's a new specialty: skills-platform engineering.

### 10. Multi-channel publishing

Distributing a skill is no longer one publish step. Each channel has its own packaging, manifest format, release process, and review queue:

- Cursor plugin
- Claude Code plugin
- Gemini CLI extension
- Codex plugin
- Our own VS Code extension
- Other secondary channels we may add

Plus the public documentation site that lists our skills and links to download or install instructions.

Each one is a release-engineering problem with its own gotchas. This step lives in software engineering, but it's a flavor of engineering most teams don't have today: you're shipping the same artifact through five different distribution surfaces, each with its own gating, signing, and update semantics.

### 11. Update the public skill catalog

Finally, the skill exists. We update our public-facing documentation to list it, link to install instructions, and describe what it does. This step is pure docs.

## The maintenance phase

Getting a skill published is the start, not the end. Skills have lifecycles in a way that documentation arguably doesn't, and in some ways more involved than software libraries do. Most of my current open questions are about maintenance.

### Re-running evals and regression tests

Every time a model vendor ships a new version, the skill's behavior may change. We need a regression cadence: run the eval suite when Claude Sonnet 4.7 ships, when GPT-6 ships, when Gemini 4 ships, and when each agent harness updates how it loads skills. Who watches model release calendars? Who triggers the regression run? Who reads the results and decides whether to flag the skill?

This is eval-engineering plus SRE work. It's recognizably software, but specifically AI/ML-flavored.

### Change governance for existing skills

If someone wants to update an existing skill, what's the process? Do they just open a PR? Do they need sign-off from the original Directly Responsible Individual (DRI)? Does an update have to traverse all the same review steps a new skill goes through? Do existing evals need to be expanded to cover the new behavior? Does a non-trivial change re-trigger routing evals? Does the public catalog page need updates?

These are governance questions that program management owns in mature engineering orgs. But "mature engineering orgs that publish skills" is currently a population of zero or very near zero. Someone has to design this process.

### Spec governance tracking

The Agent Skills spec is unversioned and ungoverned in any meaningful sense. There's no clear signal on which platforms have implemented which features at which point in the spec's history. Skills that work today on Claude Code may load differently on Cursor or Gemini CLI tomorrow because someone shipped a parser change. I've been [tracking some of this](https://agentskillimplementation.com) as a community research project, but at scale, organizations shipping official skills need someone whose job is to monitor the spec and per-platform implementations, and to flag implications for the skill collection.

This role doesn't exist anywhere I've looked. I'm doing some of it myself, in public, because no one else is.

### OWASP advisory monitoring and skill audit

The [OWASP Agentic Skills Top 10](https://genai.owasp.org/initiatives/agentic-security-initiative/) is a moving target. New advisories will land. When they do, someone has to triage them against the existing skill collection: which skills could be affected, which need updates, which need to be pulled until updated. That's a security-engineering function, with the same agent-awareness gap I noted in the security review step.

### Tool and product version tracking

A skill that supports a specific tool or product needs to be tagged with the version it represents. If the Atlas CLI skill hypothetically existed and was built against Atlas CLI 1.x, the release of Atlas CLI 2.x potentially invalidates the skill, depending on what changed. We need:

- Explicit version mapping for every skill
- A signal pipeline from product release calendars to skill maintenance
- A re-evaluation trigger when supported versions change

This is product-management work paired with release-engineering work. It overlaps with how libraries already track "supports Python 3.10 through 3.13" and bump compatibility on releases. We have less language for it in skills today. It's also not clear to me which function should own it. Docs? Engineering?

### Deprecation and model-coverage communication

When does a skill become obsolete? If we discover Claude Sonnet 4.5 needs a particular skill but Claude Sonnet 4.7 doesn't, when do we deprecate? "When most customers are on 4.7" is the right answer, but who measures that, and what counts as "most"? How do we communicate deprecation: through release notes, in-app banners, the skill's own description field, the public catalog?

This is product management plus customer communications. It's not a steady state for any function I've worked with. Most teams haven't deprecated anything in this shape before.

### User testing

After release, real users use the skill on real workflows. We learn things we couldn't learn from evals alone. Who runs the user-testing process? When user testing reveals a gap, does it route through the original DRI for changes? Does the testing team submit a formal "update request" that re-enters the lifecycle at triage? How do we keep user-testing signal from evaporating because no one downstream is staffed to act on it? How do we staff the work of making changes to existing skills?

That's a UX-research function paired with whatever change-governance process we land on paired with someone who has the capacity to action the research results and perform the updates. It barely exists as a function for skills today.

## The capability matrix

Here's the same lifecycle, viewed as a question of who's equipped to own each step. ✓ means the discipline is well-equipped. "partial" means they cover part of the step but need a partner. **Gap** means the role this step needs doesn't have a recognizable home in most org charts.

| Lifecycle step | Docs | Software eng | AI/ML eval eng | Security eng | Product / PM | UX research | Program mgmt | Gap / emerging role |
|---|---|---|---|---|---|---|---|---|
| Idea submission gating (eval design) | | | ✓ | | | | | |
| Triage (JTBD, persona, scope) | partial | | | | partial | | | **Cross-portfolio architect** |
| Skill development | ✓ writing | partial scripts | | | partial | | | |
| SME review | | ✓ | | | | | | |
| QA human review | ✓ instructional | partial workflow risk | | | | | | |
| Security review | | | | partial | | | | **Agent-aware threat modeling** |
| Functional evals | | | ✓ | | | | | |
| Routing evals | | partial | partial | | | | | **Skills-platform engineer** |
| Multi-channel publishing | | partial | | | | | | **Skills-platform engineer** |
| Public skill catalog | ✓ | | | | | | | |
| Regression eval cadence | | | ✓ | | | | partial | |
| Change governance | | | | | partial | | ✓ | |
| Spec governance tracking | | | | | | | | **Spec steward** |
| OWASP advisory tracking | | | | partial | | | | **Agent-aware threat modeling** |
| Tool/product version tracking | | partial | | | ✓ | | partial | |
| Deprecation + comms | partial | | | | ✓ | | partial | |
| User testing | | | | | | ✓ | | |

A few patterns jump out of this table.

**Documentation contributes to five steps**: the public catalog, the writing in skill development, the instructional side of QA review, and partial work in triage and deprecation comms. Non-trivial ownership, but nowhere near the whole lifecycle.

**Software engineering, in its various flavors, owns the most**. But "software engineering" here is a cluster of specialties (release, CI, security, eval, platform), several of which are emerging as their own roles.

**AI/ML eval engineering is the single discipline most central to the lifecycle**, owning three of the steps outright (idea submission gating, functional evals, regression eval cadence) and partial on a fourth (routing evals). This is the function most under-staffed in companies trying to ship official skills.

**Six gap entries cluster around four emerging roles**. None of the existing column headers cover them well. Two additional specializations of existing disciplines come up in the role list below too: they aren't gaps the matrix exposes, but they're emerging specialties most companies haven't named yet.

## Four role-shaped holes, plus two specializations

### Four new roles

These are the roles the matrix says don't have a recognizable home in most org charts today:

1. **Cross-portfolio skill triage architect.** Someone who can look at a proposal, see the whole product surface, the whole skill collection, and the customer JTBD, and say "this is actually three skills, two of which span deployment partners." Most PMs only see their corner of the product. Product architects who could see the whole thing aren't usually evaluating skill proposals. The Atlas CLI proposal is exactly the kind of input this role is meant to catch.

2. **Skills-platform engineer.** Owns the collection-level concerns: routing tests, trigger-conflict analysis across potentially hundreds of skills at scale, multi-channel publishing pipelines, distribution-channel adapters. Barely exists as a role anywhere. A handful of people in the ecosystem are doing parts of this work; I don't know anyone doing it full-time.

3. **Agent-aware security engineer.** Security review with OWASP Agentic Top 10 fluency and threat modeling for agent contexts. Most security engineers haven't internalized the agent threat model yet. Most companies shipping skills are doing security review with a generalist security team that doesn't yet know what to look for.

4. **Skill spec / standards steward.** Tracks the unversioned spec, watches per-platform implementations, files internal updates when a platform's loader changes, monitors community drift. I'm doing pieces of this in public via [skill-validator](https://github.com/agent-ecosystem/skill-validator) and [agentskillimplementation.com](https://agentskillimplementation.com), but I'm not aware of anyone funded to do it as their job.

### Two specializations of existing disciplines

These aren't gaps in the matrix; the underlying disciplines exist and own the relevant work. But the *skill-specific* flavor of each is its own emerging specialty that companies will need to name and staff explicitly:

5. **Skill eval engineer.** Eval design for skills is a subspecialty inside the broader AI/ML evaluation discipline. It needs gating-eval frameworks, cross-model panels, harness matrices, statistical thresholds, regression baselines, and the operational discipline to run all of that on a continuous cadence. It's not "QA does evals." It's a specialty.

6. **Skill change-governance program manager.** Designs and runs the process for change requests, DRI policy, regression triggers, customer comms, deprecation lifecycle. A program-management function specialized for the skills change-control problem. The maintenance open questions in this article are essentially the job description for this role.

Notice that *none* of these is "documentation." But also *none* is just "software engineering." They sit at intersections of existing disciplines, and they're new enough that most companies haven't recognized them as roles yet.

## What this means for organizations shipping skills

The position I've proposed at MongoDB, in slightly rougher form, is now this:

Skills look like markdown files. The lifecycle around shipping, supporting, and maintaining an official skill collection is *not* a markdown file. It's a multi-discipline pipeline that crosses docs, software engineering, AI/ML evaluation, security, product, program management, and UX research, and it requires several roles that don't exist in most org charts today.

Any confusion between docs and software leadership about who owns skills is symptomatic. Both functions correctly recognize they own *some* of this work. Both correctly recognize the rest doesn't fit them. Neither has the staff to cover the full lifecycle alone.

The right response isn't to pick a winner. It's to build a cross-functional team, identify the gaps, and start staffing for the emerging roles. Some of those hires will be inside engineering. Some will be inside docs. Some will be new roles with reporting lines that haven't been figured out yet. That's normal for a discipline this young.

If you're at an organization currently shipping official skills, it's worth asking: which steps in this lifecycle have a clear owner? Which have an ambiguous owner who's doing the work because someone has to? Which have no owner, and which are silently failing because of it? The answers are the starting point for staffing the next year of work.

## A note on tool-specific skills

The Atlas CLI example raises a question I haven't fully addressed: when is a tool-specific skill the right shape, versus a workflow-specific skill that may invoke that tool among others? The two have very different scoping, evaluation, and maintenance properties. I'm working through this distinction internally and will write about it in a separate article. For now, the heuristic I'm using is: if you can't articulate the failure mode the skill is supposed to fix, you don't have a skill yet, regardless of how the team feels about the underlying tool.

## What I'm building next

The lifecycle in this article is a draft, not finished work. Several pieces (gating eval framework, parts of the change governance process, the spec-governance tracking I do in public) are partially built. Others (model-coverage deprecation, the cross-portfolio triage role) are open problems I expect to keep working on as time and organizational understanding of these needs align. If you're approaching skills anything like this, I'd love to hear what you're doing similarly and what I've missed here.
