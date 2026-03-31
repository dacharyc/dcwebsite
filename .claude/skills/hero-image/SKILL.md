---
name: hero-image
description: >
  Generates Midjourney prompt options for blog post hero images on dacharycarey.com.
  Analyzes the article's thesis and recommends visual metaphors matching the site's
  established aesthetic. Use when a post needs a hero image.
compatibility: Designed for Claude Code (or similar products)
allowed-tools: Read Glob
metadata:
  author: dacharycarey
  version: "1.0"
---

# Hero Image Prompt Generator

You are generating Midjourney prompt options for hero images on dacharycarey.com, a personal blog covering AI, agent ecosystems, documentation, and software development.

## Step 1: Read the article

If the user provided a file path, read it. Otherwise, ask which post needs a hero image.

Read the full article. You need to understand the thesis, central metaphor, and emotional register to generate effective prompts.

## Step 2: Read recent hero images for visual context

Read 3-5 recent hero images from `static/images/` to calibrate against the current visual style. Choose images from posts close in date to the target article. The images are viewable directly (they are image files).

## Step 3: Identify the core visual concept

Before writing any prompts, identify:

1. **The article's central tension or thesis** — what is the piece *about* at a conceptual level?
2. **A physical metaphor** — what real-world object, scene, or situation captures that concept? The best hero images on this site are conceptual/metaphorical, not literal illustrations of the topic.
3. **The emotional register** — is the article investigative? Cautionary? Optimistic? Frustrated? The image should match.

State these three things to the user before generating prompts.

## Step 4: Generate 3-5 prompt options

Generate 3-5 Midjourney prompts that represent different visual approaches to the concept. Vary the approaches:

- At least one **photorealistic scene** (objects on a desk, an environment, a person in silhouette)
- At least one **conceptual/miniature** (the yarn-ball style — a single striking metaphor against a clean background)
- At least one **split composition or diptych** if the article has a contrast or before/after structure

### Visual style rules for this site

These are derived from the established hero image aesthetic on dacharycarey.com:

**Composition:**
- Objects as protagonists more often than people
- When people appear, they are in profile, silhouette, or from behind — secondary to the object/concept
- Shallow depth of field in photorealistic scenes
- Clean negative space in conceptual/miniature compositions

**Lighting and color:**
- Moody, warm lighting. Amber and golden tones dominate.
- Dramatic shadows, cinematic quality
- Muted, desaturated palettes with one warm accent color (amber glow, red markup, golden light, purple UV)
- Dark or neutral backgrounds preferred

**Content:**
- No text, words, letters, or labels in the image
- No UI mockups or screenshots
- No stock-photo energy (no smiling people at laptops, no handshakes, no abstract geometric patterns)
- No overt AI imagery (no glowing brains, no neural networks, no circuit boards unless the article is specifically about hardware)
- Don't overuse the magnifying glass as metaphor

**What works on this site (reference examples):**
- Redlined document with scissors on a moody desk (editorial evaluation)
- Cobwebbed filing cabinet with warm glow (staleness/decay)
- Child pulling a massive yarn ball (complexity vs. human scale)
- Split-screen desk: mystical/alchemical vs. scientific/microscope (vibes vs. data)
- Conveyor belt of news items funneling into a glowing tornado (information processing)
- Woman with magnifying glass examining a data wall (investigation/scrutiny)

### Midjourney flags

Every prompt must end with:
```
--ar 4:3 --no text, words, letters, labels --s 50
```

The site uses `--ar 4:3` due to the theme implementation, so images must work at that size. We can omit the no text flag if there is a single word that may generate well, but overall, should avoid it, as Midjourney is still bad at words.

## Step 5: Present options

Present each prompt with:
1. **A short title** (2-4 words describing the visual concept)
2. **The full Midjourney prompt**
3. **Why it works** (one sentence connecting the visual to the article's thesis)

Let the user choose. If they want variations on a specific option, generate 2-3 riffs on that concept with different compositions, lighting, or metaphor angles.

## Step 6: Update front matter

Once the user has generated their image and saved it to `static/images/`, update the post's `image:` field in the front matter to point to the new file. The naming convention is:

```
/images/<slug>-hero.jpg
```

Where `<slug>` matches the post's URL slug (e.g., `ai-content-pipelines-verification-gap-hero.jpg`).
