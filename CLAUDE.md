# dcwebsite

Hugo personal site for dacharycarey.com. Theme: `menca` (extended via project-level layout overrides in `layouts/`). Content lives in `content/`, deployed via rsync to the live server.

## Build & deploy

```
hugo server -D          # local dev with drafts visible
hugo server             # local dev, drafts hidden (matches production)
./generate_llms_txt     # regenerate static/llms.txt from current content
./build_and_sync        # full prod pipeline: llms.txt → hugo build → rsync to dacharycarey.com
hugo                    # local production build only (output in public/)
```

`build_and_sync` is the canonical deploy. It calls `generate_llms_txt` first, then `hugo`, then rsyncs `public/` to the live host. Don't run `hugo` and `rsync` separately for deploys; the llms.txt step is required.

## Content structure

- `content/posts/YYYY-MM-DD-slug.md` — published posts. Filename date prefix sorts the directory; the canonical date for sorting and URLs lives in frontmatter (`date:` and `url:`).
- `content/drafts/` — work-in-progress posts. Frontmatter has `draft: true`. Promotion to published means moving the file into `content/posts/`, setting `draft: false`, and confirming `date` / `url` are right.
- `content/{about,ai-research,contact,documentation,programming,resume}.md` — top-level pages.

Post frontmatter convention (see any recent post for an example):

```yaml
---
title: ...
author: Dachary Carey
layout: post
description: In which ... (sentence-style, becomes meta description, RSS summary, and OpenGraph)
date: YYYY-MM-DD HH:MM:SS -0500
url: /YYYY/MM/DD/slug/
image: /images/slug-hero.{png,jpg}
tags: [ai]    # or other topic tags
draft: false
---
```

## Conventions

### Zoomable diagrams and images

Use the `pswp` shortcode (`layouts/shortcodes/pswp.html`). It wraps the image in a PhotoSwipe-compatible anchor so readers can click to zoom past viewport width.

```
{{< pswp src="/images/diagrams/foo.svg" alt="thorough description for screen readers, RSS, and markdown export" >}}
```

Parameters:
- `src` — served URL (Hugo serves `static/` from site root, so `static/images/foo.svg` → `/images/foo.svg`)
- `alt` — describe the diagram thoroughly. This is the only description in RSS feeds, the markdown output format used by agents, and for screen readers. Don't skimp.
- `width` / `height` — optional, default 3500×3900. The shortcode applies these as `aspect-ratio` CSS on the `<img>` so SVGs render at correct height in Safari (which can't infer aspect ratio from SVGs that use `width="100%"` in their root).
  - **SVGs**: leave defaults. SVG scales infinitely; 3500×3900 gives generous zoom past viewport for accessibility (low-vision users can read fine detail).
  - **Raster images (PNG/JPG)**: pass actual file pixel dimensions. Raster images pixelate past native, so don't inflate.

PhotoSwipe is loaded from jsdelivr CDN (`layouts/partials/head.html` and `layouts/partials/javascripts.html`). The init script is gated on `document.querySelector('a.pswp-image')` so PhotoSwipe only loads when a page actually has a zoomable image.

### Mermaid diagrams

Mermaid source files live in `static/images/diagrams/*.mmd`. Render to SVG with the Mermaid CLI:

```
npx --yes -p @mermaid-js/mermaid-cli mmdc -i static/images/diagrams/foo.mmd -o static/images/diagrams/foo.svg
```

For wide diagrams, add `-w 1400` (or larger) so Mermaid's layout engine has room.

Embed the rendered SVG via the `pswp` shortcode (see above). Keep the `.mmd` source committed alongside the SVG so future edits don't require rebuilding from scratch.

Mermaid quirks worth knowing:
- Subgraph titles don't render bold or larger via HTML/markdown tags. If you need a styled title, use a header *node* inside the subgraph (`L0["\` **Title** \`"]:::title` with `classDef title fill:none,stroke:none`) — but be aware it consumes a rank in the layout.
- To position an unconnected subgraph relative to the main flow, use invisible edges (`~~~`) to anchor it. To create a vertical gap, add invisible spacer nodes (`SPACER[ ]:::invisible` with `classDef invisible fill:none,stroke:none,color:transparent`).
- `direction TB` inside a subgraph forces vertical stacking when the outer flowchart is also TD.

### Hero images

Hero images live in `static/images/{slug}-hero.{png,jpg}` and are referenced via frontmatter `image:`. Two production paths:

**Midjourney metaphor heroes (default).** Photographic conceptual scenes following the site's established aesthetic (warm amber lighting, dark moody backgrounds, objects as protagonists, no text or AI imagery). Use the `/hero-image` skill to generate prompt options matching the article's thesis.

**Hand-drawn diagram heroes (for articles where the diagram *is* the thesis).** Some posts pair better with a literal diagram than a metaphor — typically when the article makes a structural argument and a visual TL;DR is more powerful than a photo. Examples on this site: hand-sketched audit diagrams from 2025, the SKILL.md roles-wheel for the "A Skill is More than Markdown" post.

Workflow for SVG diagram heroes:

1. Write the SVG by hand in `static/images/diagrams/{name}.svg`. Use a 4:3 viewBox (e.g. `0 0 800 600`) so it matches hero aspect ratios. Keep stylesheet inline (`<style>` tag inside the SVG) so the file is self-contained.
2. If the hero shares semantics with a Mermaid diagram in the body, reuse the Mermaid classDef palette in the SVG so the two read as a set. Current palette for skill-lifecycle posts: pink `#fce4ec`/`#c2185b` (docs), blue `#e3f2fd`/`#1565c0` (engineering), purple `#f3e5f5`/`#6a1b9a` (eval), orange `#fff3e0`/`#e65100` (PM), green `#e8f5e9`/`#2e7d32` (UX), gray `#f5f5f5`/`#666666` (cross-functional). Fill / stroke pair per color.
3. Convert SVG to PNG via `sharp-cli`. Social-card meta tags (Twitter, OG) need raster:

   ```
   npx --yes sharp-cli --input static/images/diagrams/{name}.svg --output static/images/{slug}-hero.png resize 1600 1200
   ```

   1600×1200 gives a generous hero size that downscales cleanly. ~60–80KB typical file size.
4. Reference the PNG (not the SVG) in frontmatter `image:`. Keep the SVG source in `static/images/diagrams/` for future edits — re-run step 3 after any SVG change.

Mermaid mindmap looks like a tempting shortcut for radial diagrams, but it does not support per-node `classDef` coloring in current Mermaid versions and Mermaid flowchart layouts won't produce a true wheel. Hand-written SVG is the right tool when you need an actual radial layout with the matching palette.

### Writing style

Reinforcing the global CLAUDE.md guidance for posts on this site:

- Avoid em dashes; vary with parentheses, colons, semicolons, periods, commas. A few em dashes per article is fine, but heavy use reads as LLM-generated.
- First-person, conversational, direct. See "Can Agent Skills Make Output Worse?" or "Agent Skill Mega Repo Woes" for tone.
- Sentence-style headers, often as questions or punchy phrases.
- Link generously to her own prior work and to other authors' work.

## Tests

### afdocs against production

`tests/agent-docs.test.ts` runs Agent-Friendly Documentation Spec checks against the live site (`https://dacharycarey.com`) using the `afdocs` package (in `devDependencies`). Config in `tests/agent-docs.config.yml`. Run with:

```
npm run test:agent-docs
```

End-to-end check against production. Run after deploys.

### afdocs against the local dev server

Use this to validate infra changes (theme tweaks, new shortcodes, head/footer additions, etc.) before pushing. Faster than deploying and re-running against production.

Two prerequisites:

1. **llms.txt must be regenerated.** It's a build-time artifact, normally produced by `./generate_llms_txt` (called automatically by `build_and_sync`). The dev server doesn't auto-regenerate it. If you've added a new post and want it counted in `llms-txt-coverage`, run `./generate_llms_txt` first. Hugo serves `static/llms.txt` directly without rebuild.
2. **Dev server must be running.** `hugo server` for published content, `hugo server -D` to include drafts.

Then run afdocs from the local checkout (preferred during development of afdocs itself) or via the npm-installed version. The local checkout is at `/Users/dachary/workspace/agent-ecosystem/afdocs/`:

```
# Full site check
node /Users/dachary/workspace/agent-ecosystem/afdocs/bin/afdocs.mjs check http://localhost:1313 --canonical-origin https://dacharycarey.com

# Single page (much faster, for iterating on one post)
node /Users/dachary/workspace/agent-ecosystem/afdocs/bin/afdocs.mjs check http://localhost:1313/YYYY/MM/DD/slug/ --sampling none --canonical-origin https://dacharycarey.com

# Specific checks only
node /Users/dachary/workspace/agent-ecosystem/afdocs/bin/afdocs.mjs check http://localhost:1313 --canonical-origin https://dacharycarey.com --checks rendering-strategy,page-size-html,content-start-position
```

`--canonical-origin https://dacharycarey.com` is required because Hugo embeds the production origin in generated content (URLs in `llms.txt`, sitemap, internal links). Without it, every same-origin comparison fails and checks like `llms-txt-coverage` report 0%.

Known expected behavior locally (not real issues):

- **`content-negotiation` always fails locally.** Hugo's dev server doesn't honor `Accept: text/markdown`; it serves by extension only. This is also true of production (rsync to a static host), so the failure is currently a known limitation of the deploy mechanism, not a bug to fix unless you swap hosting to something with content-negotiation support (Cloudflare Workers, nginx with rules, etc.).
- **Some checks may behave differently than production.** Cache headers and redirect behavior in particular. See `/Users/dachary/workspace/agent-ecosystem/afdocs/docs/run-locally.md` for the full list of local-vs-production differences.

When checking after infra changes, the key checks to watch are:

- `rendering-strategy` — confirms content is server-rendered (no JS-only content)
- `content-start-position` — confirms main content appears early in HTML (no nav/template bloat pushing it down)
- `page-size-html` / `page-size-markdown` — confirms the page hasn't been bloated
- `markdown-content-parity` — confirms HTML and markdown export agree (alt text on images counts here; the `pswp` shortcode preserves alt text in both outputs)
- `markdown-code-fence-validity` — confirms code fences are properly closed

## Layout overrides

Project-level overrides (in `layouts/`) take precedence over the menca theme:
- `layouts/partials/head.html` — adds PhotoSwipe CSS and custom.css
- `layouts/partials/javascripts.html` — adds PhotoSwipe init module + ionicons
- `layouts/partials/llms-directive.html` — emits the LLMs.txt-related meta directive
- `layouts/_default/baseof.html`, `single.md`, `section.md`, `taxonomy.md`, `term.md` — markdown output format templates for the `index.md` files referenced in `llms.txt`
- `layouts/shortcodes/pswp.html` — the zoom shortcode

Theme updates are safe — these overrides will keep working.

## External runtime dependencies

- **PhotoSwipe v5.4.4** via jsdelivr CDN (CSS + ESM modules)
- **Ionicons 5.5.2** via unpkg CDN
- **Google Fonts (Jost)** via fonts.googleapis.com

Self-hosting any of these is a swap of the URL in `head.html` or `javascripts.html`. PhotoSwipe files for self-host: `photoswipe.css`, `photoswipe-lightbox.esm.js`, `photoswipe.esm.js` (~50KB total).
