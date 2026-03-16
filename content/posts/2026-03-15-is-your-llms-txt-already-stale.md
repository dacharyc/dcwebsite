---
title: Is Your llms.txt Already Stale?
author: Dachary Carey
layout: post
description: In which I build a freshness check for llms.txt and discover that my tools were the problem.
date: 2026-03-15 23:00:00 -0500
url: /2026/03/15/is-your-llms-txt-already-stale/
image: /images/llms-txt-freshness-hero.jpg
tags: [ai, documentation]
draft: false
---

You shipped your `llms.txt`. You linked to your docs pages. Maybe you even set up progressive disclosure with per-product files. You're done, right?

Probably not. An `llms.txt` that was accurate when you launched it can silently drift out of sync with your actual documentation. New pages get added to the site but not to `llms.txt`. Old pages get removed or reorganized. The file becomes a stale index that points agents toward an incomplete picture of your docs, or worse, toward pages that no longer exist.

This is the problem the [`llms-txt-freshness` check](https://agentdocsspec.com/spec/#llms-txt-freshness) in the [Agent-Friendly Docs Spec](https://agentdocsspec.com) is designed to catch. I spent the weekend implementing it in [afdocs](https://www.npmjs.com/package/afdocs), and what I found while testing against real documentation sites was more interesting than the implementation itself. The short version: most of the initial failures turned out to be bugs in my tool and stale URLs from my agent, not gaps in the docs ecosystem. And the sites where the check *did* work revealed some surprising patterns in how teams approach `llms.txt`.

## How the Check Works

The concept is simple. Compare the pages listed in `llms.txt` against the pages in the site's sitemap. Pages in the sitemap but not in `llms.txt` are coverage gaps. Pages in `llms.txt` but not in the sitemap are potentially stale (or the sitemap is incomplete, which is its own problem).

The implementation is less simple. The check has to:

1. Walk `llms.txt` to extract all page URLs, including following progressive disclosure links to per-product `llms.txt` files
2. Discover the sitemap (via `robots.txt`, then falling back to `{baseUrl}/sitemap.xml`)
3. Scope the sitemap to the docs path prefix (you don't want to compare against marketing pages)
4. Exclude non-doc paths like `/blog`, `/changelog`, `/pricing`, `/careers`
5. Normalize both URL sets (strip `.md`/`.html` extensions, trailing slashes, `/index` suffixes)
6. Compare

Coverage is the percentage of sitemap doc pages that appear in `llms.txt`. The spec defaults are: pass at 95%+, warn at 80-95%, fail below 80%. Pages in `llms.txt` but not in the sitemap are reported as informational only, because they might be stale or the sitemap might just be incomplete. There's no way to distinguish without fetching every URL, and `llms-txt-links-resolve` already handles that.

Simple enough in theory. Then I tested it against 10 real documentation sites.

## First Run: Almost Nothing Works

My first run produced results on only 2 of 10 sites. The other 8 skipped because the check couldn't find any sitemap URLs under the docs path prefix.

| Site | Status | Notes |
|------|--------|-------|
| Resend | fail | 100% coverage but 36% "stale" (misleading framing) |
| Stripe | fail | 26% coverage; 87% "stale" |
| Cloudflare | skip | Sitemap index not fully traversed for /workers/ prefix |
| Supabase | skip | Docs in child sitemap; main sitemap had marketing only |
| Loops | skip | Docs sitemap exists but not listed in robots.txt |
| Neon | skip | Cross-host redirect; sitemap at redirected origin not found |
| MongoDB | skip | Complex 3-level sitemap index hierarchy |
| Anthropic | skip | Cross-host redirect; no sitemap found |
| Vercel | skip | Docs URLs exist but fell past the tool's 500-URL cap |
| Daytona | skip | Sitemap referenced in robots.txt returns 404 |

The failure was "sitemap has N URLs but none match docs path prefix." My first instinct was that docs sitemaps are a mess. After investigating all 10 sites, I discovered that most of the failures were on my side.

## It Wasn't the Sitemaps

The [sitemaps.org protocol](https://www.sitemaps.org/protocol.html) defines two discovery mechanisms: listing sitemaps in `robots.txt`, and placing a sitemap at `/sitemap.xml`. It also defines sitemap index files, where a sitemap lists child sitemaps rather than URLs directly. Between these three mechanisms, a crawler should be able to find every URL a site wants indexed. When I dug into each site's actual sitemap setup, most of them were doing things right. The problems were in my tools.

### Tool bugs: 3 of 10

**Supabase** has a root sitemap index with two children: a marketing sitemap (zero `/docs/` URLs) and a docs sitemap at `/docs/sitemap.xml` (1,100+ URLs). This is fully protocol-compliant: `robots.txt` points to the index, the index lists child sitemaps, and the docs child contains the URLs. The tool just wasn't traversing all children correctly.

**Cloudflare** uses a dedicated docs subdomain (`developers.cloudflare.com`) with a sitemap index. All URLs are docs, but when scoping to a specific product like `/workers/`, the index's child sitemaps weren't all getting traversed. Same class of bug as Supabase.

**Vercel** looked like it had no docs in its sitemap at all. The initial implementation capped sitemap parsing at 500 URLs (a limit designed for page sampling in other checks), and Vercel's sitemap has nearly 5,000 URLs. The `/docs/` URLs come after `/academy/`, `/ai-gateway/`, `/blog/`, and `/changelog/` alphabetically, so they all fell past the cap. The tool saw 500 URLs, none of them docs, and concluded there was nothing to compare against.

Amusingly, I hit the same problem in a different form while writing this article. When I had Claude Code use a web fetch tool to investigate Vercel's sitemap, Claude reported that the 692KB XML file contained only the first ~1,063 URLs: academy, AI gateway, and blog entries. No docs. Claude confidently reported "zero `/docs/` URLs" until I pointed out that a 692KB sitemap probably had more in it than Claude was seeing, and asked it to `curl` and `grep` for docs urls.

The result confirmed my suspicion: 1,464 `/docs/` URLs that the web fetch had silently dropped. The same class of problem (truncated content leading to wrong conclusions) that the Agent-Friendly Docs Spec is designed to catch, showing up in the process of building the tool that implements the spec. Claude did not have any indication that the result had been truncated, and it was only my prior experience with this issue and noting the size of the fetch that made me ask Claude to try a different method to retrieve the data. Claude glibly insisted that the sitemap had no docs URLs in it and we should move on, unaware it was seeing only a subset of the data.

### Stale training data: 2 of 10

**Neon** migrated from `neon.tech` to `neon.com`. Every path on the old domain 308-redirects to the same path on the new domain. The `llms.txt` at `neon.tech/docs/llms.txt` redirects to `neon.com/docs/llms.txt`, and all links inside point to `neon.com`. But the freshness check was looking for a sitemap on the original `neon.tech` origin, and nothing matched.

Here's the thing: the only reason the cross-host redirect was involved at all is that Claude was running the tool. I had Claude run afdocs against a batch of docs sites, and it used `neon.tech` as the base URL because that's what's in its training data. If I'd gone to run the tool myself, I would have looked up the domain in a search engine, found `neon.com/docs`, and never hit the redirect. This is the same [stale URL from training data](/2026/02/18/agent-friendly-docs/) pattern I've observed repeatedly: agents reach for URLs they learned during training, and when those URLs have moved, the agent doesn't know. Neon's redirects are clean (every path 308s correctly), so the content still resolves. But the tool's sitemap discovery broke because the origin it was working with wasn't the canonical one.

It's a neat illustration of how domain migrations ripple outward. Even when your redirects work perfectly for content, downstream tooling that compares origins can still trip over the mismatch. And when the thing running your tooling is an agent with stale training data, it will find the old domain every time.

**Anthropic** has a more complex redirect situation. `docs.anthropic.com` redirects to `platform.claude.com/docs/`, but the redirect rule prepends `/docs/` to every path. So `docs.anthropic.com/robots.txt` redirects to `platform.claude.com/docs/robots.txt`, which is a 404, not `platform.claude.com/robots.txt`, which actually exists. Same root cause as Neon (Claude used the old domain from training data), but a messier redirect structure that compounds the problem.

### Genuine ecosystem gaps: 3 of 10

After accounting for tool bugs and stale training data, only 3 sites had issues that were genuinely on the sitemap side.

**Loops** has a main `sitemap.xml` listed in `robots.txt` with 161 marketing URLs. Zero docs pages. But a separate `/docs/sitemap.xml` exists with 167 doc URLs. It's just not referenced from `robots.txt` or the main sitemap index. If you're a crawler following the protocol's discovery paths, you wouldn't see it.

**Daytona** references a sitemap in `robots.txt`, but the URL returns 404. A docs sitemap *does* exist at `/docs/sitemap-index.xml` (a sitemap index pointing to `/docs/sitemap-0.xml` with ~380 URLs). But that path isn't one the protocol defines, and it's not referenced from `robots.txt`. The tool's fallback tries `{baseUrl}/sitemap.xml` as a heuristic beyond the protocol, but `/docs/sitemap-index.xml` is a different convention entirely. So there's a working sitemap with real doc URLs but it wasn't at the first location Claude reached for.

**MongoDB** has a 3-level sitemap hierarchy that I'll cover in more detail later. It's a genuine outlier, and the most complex sitemap structure I found in the survey.

### The actual scorecard

Protocol-level discovery would have worked for 6 of 10 sites if the tool had handled it correctly. The tool's own bugs accounted for half of the initial failures. After fixing those bugs, adding a `{baseUrl}/sitemap.xml` heuristic for undiscoverable docs sitemaps, and adding cross-host redirect support for stale agent URLs, the check produces results on 8 of 10 sites.

The lesson here wasn't "docs sitemaps are a mess." It was "check your own tools before blaming the ecosystem." Most of these sites had perfectly reasonable sitemap setups. But my Claude-written check code and Claude-supplied docs URLs were missing them.

## After the Fixes: Real Patterns Emerge

Fixing the tool bugs and adding cross-host redirect support got sitemap discovery working on most sites. Two more fixes were needed to get accurate *results* from the sites we could now reach.

**Locale filtering.** Even after fixing sitemap discovery, Anthropic's coverage showed 31% when it should have been 100%. The sitemap includes pages in 9 languages, but `llms.txt` only covers the English variants. The check now detects locale patterns empirically: it scans path segments at each position, identifies positions where multiple distinct 2-letter codes appear, and filters the sitemap to match `llms.txt`'s dominant locale. For Anthropic, position 1 after `/docs/` has 9 distinct codes; the `llms.txt` is 100% `en` at that position, so the sitemap filters to English-only. After filtering: 100% coverage. My very experienced in web dev wife pointed out this only covers one locale filtering pattern, so there are likely other bugs waiting in the wings.

**Reframing "stale" as "unmatched."** Resend's initial result showed 100% coverage but 36% "stale" links. The 193 `llms.txt` URLs that my tool found that were not in the sitemap aren't stale pages. They're knowledge-base articles and SDK-specific pages that the sitemap doesn't list. The site's `llms.txt` is actually *more* comprehensive than its sitemap. Reporting these as "stale" was misleading, so the check now reports them as informational without affecting the overall status.

With all fixes applied, the check produces results on 8 of 10 sites:

| Site | Status | Coverage | Sitemap Source | Doc Pages | Notes |
|------|--------|----------|----------------|-----------|-------|
| Cloudflare | pass | 100% | /workers/sitemap.xml | 430 | Progressive disclosure working |
| Loops | pass | 100% | /docs/sitemap.xml | 131 | Unlisted docs sitemap discovered via fallback |
| Resend | pass | 100% | robots.txt | 255 | 193 unmatched = sitemap incomplete, not stale |
| Anthropic | pass | 100% | robots.txt | 651 | Locale filtering: en at position 1 |
| Vercel | warn | 83% | robots.txt | 1,431 | Now producing results |
| Neon | fail | 31% | robots.txt | 485 | 246 changelog excluded via base-path matching |
| Supabase | fail | 22% | /docs/sitemap.xml | 2,008 | Genuine gap |
| Stripe | fail | 15% | robots.txt | 3,037 | Genuine gap |
| MongoDB | skip | — | — | — | 3-level sitemap hierarchy; depth limit |
| Daytona | skip | — | — | — | Sitemap at path my tool didn't check; not discovered |

Now it gets interesting. The passing sites, the warning, and the three failing sites tell different stories about how teams are using `llms.txt`. And those stories have implications for how agents discover and navigate documentation.

## Two Camps: Link Index vs. Content Dump

I was surprised to find that `llms.txt` implementations seem to have split into two different approaches that serve different agent architectures.

**The link index pattern** (Cloudflare, Loops, Resend) treats `llms.txt` as a structured index of individual page links. Cloudflare does this with progressive disclosure: the root `llms.txt` links to 130 per-product `llms.txt` files, each of which links to actual `.md` page URLs. An agent can discover the product it cares about, drill into that product's `llms.txt`, and fetch the specific pages it needs. Loops and Resend link directly to individual pages from a flat `llms.txt`.

**The content dump pattern** (Supabase) treats `llms.txt` as a gateway to monolithic content files. Supabase's `llms.txt` links to 8 aggregate `.txt` files (`guides.txt`, `js.txt`, `dart.txt`, etc.). These aren't page indexes. They're full documentation dumps. `guides.txt` is 4.4MB of inline markdown containing the complete text of all guides. `js.txt` is 47KB of full JavaScript SDK reference. The content is there, but individual pages aren't discoverable as discrete URLs.

Supabase has clearly invested in their `llms.txt` (8 aggregate files, 4.4MB+ of content). They've optimized for the "dump everything into a context window" pattern, where an LLM loads an entire file and answers questions from it. But this doesn't work for coding agents that need to discover and fetch specific pages during a real-time development workflow. When a developer using Claude Code asks about Supabase auth, the agent needs to find the auth page, not download 4.4MB of every guide on the site. And if it's Claude Code using a web fetch to retrive the data, it will get only a tiny subset of that 4.4MB file and never even know it was truncated.

The [llms.txt proposed spec](https://llmstxt.org) defines the format as a curated link index: "Zero or more markdown sections delimited by H2 headers, containing 'file lists' of URLs where further detail is available." Full content dumps are what the community calls `llms-full.txt` or `llms-ctx-full.txt`, derived outputs produced by tools like `llms_txt2ctx`. They serve a different purpose.

The 22% coverage result for Supabase is technically accurate. Supabase's sitemap has 2,008 doc pages. Only 435 of the URLs discoverable through `llms.txt` match sitemap entries. The other 1,573 pages, spanning guides, AI docs, API references, and integration pages, have some of their content available inline in the aggregate files, but aren't individually navigable from `llms.txt`. An agent using `llms.txt` as a page discovery index can't find them.

Both approaches serve agents. But they serve different agent architectures. If you're building your `llms.txt`, the question to ask is: do you expect agents to load a large chunk of content at once, or do you expect them to navigate to specific pages? For coding agents, which are the primary consumers I've been deep diving on lately, the link index pattern is more aligned with how they actually work.

## The Stripe Puzzle: When 15% Isn't Neglect

Stripe's result was the most interesting to investigate. With 15% coverage (468 of 3,037 doc pages), the check reports a fail. But I know Stripe is lauded for its docs, and found it implausible that Stripe would have such a big miss here. So I tested a theory: maybe Stripe's `llms.txt` isn't stale. It's curated.

Only 17 of Stripe's 491 `llms.txt` URLs don't appear in the sitemap, which means `llms.txt` is an almost perfect subset of the site. I considered: maybe it's not that pages fell out of the index or that the file stopped being maintained. Maybe someone chose these 491 pages as the ones worth surfacing. Maybe following links from these pages actually reaches the rest of the sitemap without being a link dump.

The question is whether curation at this scale actually works for agents. If `llms.txt` covers the top-level pages and those pages link to deeper content, an agent might be able to reach most of the site within one or two hops. So I tested it.

I fetched all 491 `llms.txt` pages and analyzed their outbound links. A sample told the story clearly: `/payments/accept-a-payment.md` alone has 83 unique outbound doc links, 38 of which point to pages not in `llms.txt`. All 38 are real sitemap pages. The curated pages are genuinely functioning as entry points.

But the full 1-hop reachability analysis paints a more complicated picture:

| Metric | Count | % of sitemap |
|--------|-------|--------------|
| Direct (in llms.txt) | 491 | 16.1% |
| Additional via 1-hop | 1,135 | 37.4% |
| **Total reachable** | **1,623** | **53.4%** |
| Still unreachable | 1,414 | 46.6% |

Even with generous 1-hop counting, nearly half of Stripe's sitemap pages aren't reachable from an `llms.txt` entry point. The unreachable pages are concentrated in deep API reference (`/api/`, 883 pages), individual method docs (`/js/`, 269 pages), and deep sub-guides across payments, connect, billing, and more.

This isn't a knock on Stripe's approach. After investigating, I do believe it's deliberate curation, not neglect. But from an agent's perspective, a curated 500-page index into a 3,000-page site leaves a real discovery gap, especially for the long tail of API reference and method docs that agents are most likely to need for code generation tasks. An agent working on a Stripe integration that needs to check the behavior of a specific API endpoint may not be able to find that endpoint's documentation through `llms.txt` at all.

The 15% coverage correctly identifies that most docs pages aren't surfaced through `llms.txt`. Site owners who intentionally curate can treat the fail as informational. But the 1-hop analysis shows that curation alone doesn't bridge the gap. If you're curating your `llms.txt` rather than listing every page, it's worth thinking about whether the pages you omit are pages agents actually need.

## The MongoDB Sitemap: When You're the Outlier

MongoDB is one of two sites that still skip the freshness check entirely, and the investigation into why turned up some interesting patterns about how the industry handles versioned documentation.

MongoDB's sitemap hierarchy is three levels deep:

1. `robots.txt` points to `sitemap.xml`
2. `sitemap.xml` is a sitemap index with 15 children, including `docs/sitemap.xml` (which returns 404) and `docs/sitemap-index-full.xml` (which is itself another sitemap index)
3. `docs/sitemap-index-full.xml` contains 94 child sitemaps covering every product and version

The check follows two levels of sitemap indexes, which is enough for every other site I tested. MongoDB's third level is where the actual page URLs live, so they're never found.

Even if I added depth-3 traversal, the results would be misleading. Those 94 child sitemaps include every version of every product: `docs/manual/sitemap-0.xml` (current), `docs/v8.0/sitemap-0.xml`, `docs/v7.0/sitemap-0.xml`, `docs/drivers/node/current/sitemap-0.xml`, `docs/drivers/node/v6.x/sitemap-0.xml`, and so on. The `llms.txt` covers approximately 22,000 URLs for current versions. The full sitemap likely has more URLs across all versions. If the following all the child sitemaps leads to URLs in older versions that aren't listed in llms.txt, the pages would show as "missing from `llms.txt`," producing artificially low coverage percentages.

To better understand the landscape, I looked at how other major documentation sites handle versioned docs in their sitemaps:

| Site | Versioned? | Sitemap strategy |
|------|-----------|-----------------|
| AWS | Yes | Only sitemaps `/latest/` paths; old versions blocked in robots.txt |
| Google Cloud | Yes | robots.txt Allows only `/latest/` for SDK reference; blocks other versions |
| Terraform | Yes | Only sitemaps `/latest/` alias; no version numbers in sitemap |
| Python | Yes | Old versions Disallowed in robots.txt |
| Kubernetes | Minimal | Old versions Disallowed in robots.txt |
| Django | Yes | Sitemaps every version from 0.95 to 6.0 dev (only outlier that does this) |

Other versioned docs sites in the industry seem to solve versioning at the sitemap/robots.txt level: only sitemap current versions, and use `robots.txt` Disallow rules to keep old versions out of indexes. MongoDB's approach of sitemapping every version of every product across 94 child sitemaps is unique among the sites I surveyed.

There's also a straightforward bug: the root sitemap index references `docs/sitemap.xml`, but that URL returns 404. The real index is at `docs/sitemap-index-full.xml`, which is non-standard and not independently discoverable. If MongoDB fixed the 404 and consolidated to a two-level hierarchy covering only current versions, the freshness check would work automatically.

## What This Means for Documentation Teams

If you maintain a docs site and you have (or are planning) an `llms.txt`, here's what I'd take away from this.

**Check your sitemap hygiene.** Some of the sites I tested had docs sitemaps that weren't discoverable through the `robots.txt` path, or in a conventional location, using a conventional name. This affects more than just freshness checks. Search engines and other tools that rely on standard sitemap discovery may also be missing your docs pages. If your docs are on a shared domain with marketing content, make sure either the root sitemap index includes a child sitemap for your docs, or your `robots.txt` references it directly.

**Decide what pattern you're building.** If you want agents to navigate to specific pages (which is how coding agents work today), structure your `llms.txt` as a link index with progressive disclosure. If you want agents to load large chunks of content at once, that's a valid use case, but consider putting that content in `llms-full.txt` and keeping `llms.txt` as a navigable index. And keep in mind that coding agents have truncation limits when they fetch content, and the `llms-full.txt` may be far too large to be visible to them.

**Think about coverage gaps.** If you're curating your `llms.txt` rather than listing every page, check whether the pages you're omitting are pages agents actually need. API reference and method-level docs are among the most valuable pages for coding agents doing code generation. Stripe's curation covers the top-level entry points but leaves 46.6% of the site unreachable even with 1-hop navigation. That's a deliberate tradeoff, but it's one worth making consciously.

**Keep `llms.txt` updated.** This was the original motivation for the freshness check, and it's still the simplest advice. If you're adding new docs pages, add them to `llms.txt` too. If you're reorganizing your docs, update the links. Treat it like your sitemap: a living document that reflects the current state of the site, not a launch artifact.

**If you have versioned docs, only sitemap current versions.** Following the pattern used by AWS, Google Cloud, Terraform, and most other major docs sites: use a `/latest/` or `/current/` alias in your sitemaps and Disallow older versions in `robots.txt`. This keeps your sitemap manageable for tools that consume it and makes freshness checks produce meaningful results.

You can run the freshness check yourself with [afdocs](https://www.npmjs.com/package/afdocs). Point it at your docs site and see what it finds. If the check skips because your sitemap isn't discoverable, that's a finding too.
