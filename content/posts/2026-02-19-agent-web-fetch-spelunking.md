---
title: Agent Web Fetch Spelunking
author: Dachary Carey
layout: post
description: In which I ask an agent to help me explore Web Fetch with docs pages - and get a surprise.
date: 2026-02-19 21:00:00 -0500
url: /2026/02/19/agent-web-fetch-spelunking/
image: /images/agent-web-fetch-hero.png
tags: [Documentation]
draft: false
---

After posting about [Agent-Friendly Docs](https://dacharycarey.com/2026/02/18/agent-friendly-docs/), I got some very good questions that I wanted to dig into. I love the documentation community - such thinkers! So today, I present my spelunking in the agent Web Fetch tool. My goal was to figure out if I could provide a quick answer to whether agent platforms document the truncation limits of their various web fetch implementations, so documentarians can try to wrap our heads around what makes "agent-friendly docs." First criteria: agent must be able to actually get the docs content.

## Why Markdown Instead of HTML

I've heard various reasons why we should provide markdown-friendly versions of HTML pages; mainly boiling down to the fact that HTML is verbose and difficult to parse, while markdown has a higher semantic density. In other words, when the content is markdown, the agent can get the valuable content with many fewer tokens.

When I started chatting with Claude about how Web Fetch works, it told me this:

> The tool fetches the URL, converts HTML to markdown, then processes the content through a smaller/faster AI model using the prompt I provide. What I get back is that model's summarized/extracted response, not the raw page content.
> Key limitations for your use case:
> - The content may be summarized if the page is large
> - I don't get the raw HTML or exact markdown conversion directly
> - I get the smaller model's interpretation of the content, filtered through whatever prompt I give it

So of course I had to see what this looked like in practice.

### Fetching a Short Page

I started by giving Claude the markdown version of a small-ish docs page. I picked a page at random from the docs - the [create Event reference page](https://www.mongodb.com/docs/manual/reference/change-events/create/). It's not a lot of content, but it is in a table format, so I thought that might be an interesting test.

I gave Claude a link to [the markdown version of the page](https://www.mongodb.com/docs/manual/reference/change-events/create.md), and asked it to make a best effort to write exactly what it got back, verbatim, to a file in my project. Claude sent this prompt to the summarization model:

> Return the complete content of this page exactly as you see it. Do not summarize, interpret, or omit anything. Reproduce every heading, paragraph, code block, table, link, and piece of text verbatim. Include all navigation elements, breadcrumbs, sidebars, and footer content if present. Do not add any commentary, analysis, or headers of your own. Just output the raw content.

What I got back was an exact copy of the markdown docs page - a diff revealed no change. Great! Web Fetch with markdown on a small-ish page worked well.

Then, I gave Claude a link to the HTML version of the page, and asked it to make a best effort to write exactly what it got back, verbatim, to a second file in my project. Claude sent a similar prompt to the summarization model:

> Return the complete content of this page exactly as you see it. Do not summarize, interpret, or omit anything. Reproduce every heading, paragraph, code block, table, link, and piece of text verbatim. Include all navigation elements, breadcrumbs, sidebars, and footer content if present.

This is what it got back:

```markdown
# Analysis of Web Page Content

The provided content is a **CSS stylesheet dump** from a MongoDB documentation website. It contains:

## Key Components:

### 1. **Font Definitions**
- Multiple `@font-face` declarations for:
  - Source Code Pro (weights: 300, 400, 500, 700)
  - Font Awesome 5 (Brands, Regular, Solid)
  - Font Awesome 4.7.0
  - icomoon icons
  - Custom icon fonts (MMSIcons, MMSOrgIcons, ChartsIcons)

### 2. **CSS Reset & Normalization**
- Bootstrap v3.0.0 base styles
- HTML5 semantic element styling
- Box-sizing resets
- Form element normalization

### 3. **Design System Variables (CSS Custom Properties)**
~~~css
:root {
  --black: #001e2b;
  --mongodb-green: #13aa52;
  --blue-base: #016bf8;
  --sans-serif: "Euclid Circular A", "Helvetica Neue", sans-serif;
  --monospace: "Source Code Pro", monospace;
}
~~~

### 4. **Theme Support**
- Light theme (default)
- Dark theme with CSS variables for:
  - Background colors
  - Text colors
  - Navigation colors
  - Link colors

### 5. **Icon Libraries**
- Font Awesome icons (1000+ icon definitions)
- Custom MMS (MongoDB Monitoring Service) icons
- Charts and organizational icons

### 6. **Layout & Typography**
- Container max-widths (750px, 970px, 1170px)
- Document styling for API documentation
- Code highlighting styles
- Print media queries

## Purpose
This stylesheet serves MongoDB's documentation portal, providing comprehensive styling for technical documentation with support for light/dark themes, extensive icon support, and responsive design.
```

Given what Claude explained about how its Web Fetch tool works, I think we're seeing exactly the case it described; the background agent got the content, it led with a boatload of CSS, and the background agent summarized it as _a description of the CSS_. The background agent thought the **page** was just a style sheet. Nowhere on the page was any of the reference information, or even the _operation_ the page describes.

So if you _only_ serve HTML versions of your page, this may be what Claude is seeing.

This made me curious about how many characters the smaller summarization model would have had to go through to find the content, so I curled the page locally and found the opening line. It was 66 lines, or 441k characters, down the HTML response before the actual content started. The total page length as HTML was only 97 lines. The summarization model couldn't make it past 66 lines of CSS to even have a hope of guessing that there was documentation content on the page. And with the shortness of the content relative to the boilerplate of CSS, it was 87% down the page that the actual content started.

### Fetching a Long Page

Of course now I wanted to know how a longer page would work out. So I sent Claude to [the Search quick start](https://www.mongodb.com/docs/atlas/atlas-search/tutorial/). This page renders reasonably well to a human with drop-down selectors to choose the options you want to view. But I know from my recent spelunking that the markdown version of the page just flattens the entire thing into one massive too-long blob, so I was curious what the summarization agent would report.

Claude sent the same prompt to the summarization model as before:

> Return the complete content of this page exactly as you see it. Do not summarize, interpret, or omit anything. Reproduce every heading, paragraph, code block, table, link, and piece of text verbatim. Include all navigation elements, breadcrumbs, sidebars, and footer content if present. Just output the raw content.

Claude faithfully wrote what it got back from the summarization agent to a file I could inspect... and the file was 266 lines long. This was Claude's assessment of the content:

```markdown
This one came through well. The markdown source gave a clean, complete tutorial page with all the code examples, expected outputs, and learning resources. You can diff it against the actual .md source to see what (if anything) the summarization model altered or dropped. Given the short page was nearly verbatim, I'd expect this one is close too, though it's a longer page so there's more room for subtle omissions.
```

I copied the actual markdown output from the page into a file for comparison... and the file was 6,431 lines long. No way was I going to attempt to diff this. So I asked Claude to try to identify what content got included and what got omitted, and this was it's assessment:

```markdown
The original page is a tabbed tutorial. On the rendered website, you pick your driver and see only that version. But in the raw markdown, all the variants are serialized sequentially using `<Tab>` and `<Tabs>` components. The same tutorial is repeated 11 times for different drivers and clients:

| Section | Lines | Driver/Client |
|---------|-------|---------------|
| 1 | 1–525 | Atlas UI (JSON editor) |
| 2 | 525–930 | mongosh |
| 3 | 930–1452 | Compass |
| 4 | 1452–2080 | C# / .NET |
| 5 | 2080–2743 | Go |
| 6 | 2743–3405 | Java (Maven/Gradle) |
| 7 | 3405–3956 | Kotlin |
| 8 | 3956–4736 | Node.js |
| 9 | 4736–5301 | Python (PyMongo) |
| 10 | 5301–6029 | Rust |
| 11 | 6029–6431 | Docker / self-managed |

There are 93 `<Tab>` elements in total across the page, including nested tabs within sections for things like Atlas Cluster vs. Local Deployment setup, or Maven vs. Gradle for Java.

The 266-line summary that the summarization model returned corresponds closely to the intro text plus section 2 (mongosh), which is the first section that contains actual runnable code. The summarization model received the beginning of the 258K-character file, hit its context limit, and produced a response based on what it could see. It grabbed the intro and one driver variant, and that was all it could fit.
```

Claude's assessment that the content "came through well" was correct in that the content it received was clean and accurate. But it had no way of knowing that it was only seeing a fraction of the page. The 266 lines it got back represent roughly 3.3% of the total content (8.5K out of 258K characters), covering 1 of 11 driver-specific tutorials.

This is a significant finding for documentarians. When your page uses tabs or other UI patterns that flatten into long sequential content in the source, agents will only see whatever fits within the summarization model's context window. For this page, that meant a Python developer asking an agent about this tutorial would get the mongosh version instead, because mongosh happened to come first in the source order. Some subset of driver variants were completely invisible to the agent.

#### But What If You Ask for Python Specifically?

Of course, this raised the obvious follow-up question. When Web Fetch sends a prompt to the summarization model, the agent can include context about what it's looking for. So what happens if the prompt specifically asks for Python/PyMongo content?

I asked Claude to fetch the same page, but this time pass the context that we're looking for Python code. Claude sent this prompt to the summarization model:

> I am a Python developer using PyMongo. Return the complete content of this page that is relevant to Python/PyMongo. Include all Python code examples, setup instructions for Python, and expected outputs. Reproduce the content verbatim - do not summarize or omit anything.

Here's what the summarization model returned:

```markdown
# MongoDB Search Quick Start - Python/PyMongo Content

Unfortunately, the provided web page content does not include any Python or
PyMongo-specific sections.

The page includes tabbed content for multiple client libraries with the
following sections visible:

1. **Atlas Cluster** - with tabs for:
   - Atlas UI (Search Tester)
   - mongosh (MongoDB Shell)
   - MongoDB Compass
   - .NET/C#
   - Go

2. **Local Deployment** - with similar tabs

The Python/PyMongo tab content is **not present** in the provided page excerpt.
The content appears to have been truncated before reaching the Python driver
examples.
```

This is actually more interesting than I expected. The summarization model correctly identified that Python content wasn't in what it could see. It noticed tabs for mongosh, Compass, .NET/C#, and Go, but not Python. It even acknowledged that "the content appears to have been truncated before reaching the Python driver examples."

So the model is somewhat aware that it's working with incomplete content. But the end result is the same: a Python developer gets no Python code. Based on the tab listing in the response (through Go), the model saw roughly through section 5 (Go, ending around line 2743) before running out of context. The Python section doesn't start until line 4736, well beyond reach.

The ordering of tabs in your source markdown matters more than you might think. Whatever comes first is what the agent sees. Everything else might as well not exist. And asking for something specific was not able to retrieve it if the thing you're asking for was beyond the truncation limit. There was no "smart parsing" or search looking for specific elements in the un-truncated content and then returning only the relevant elements.

### Short vs. Long Page Disconnect

Which of course leads me to point out, if the summarization agent could see all the way to line 2743 in the markdown version of this long page, it seemed nonsensible that it "couldn't see" the 97 lines of the HTML version of the short page.

But line count is misleading here, and that's worth calling out. If you're a documentarian inspecting your page source and you see a small number of lines, you might think you're safe. You're not. Those 97 lines of HTML were _massive_. When I compared the actual character counts, the picture flipped completely:

- **Short page HTML** (the one where the model only saw CSS): **505,181 characters**
- **Long page markdown through line 2743** (where the model stopped seeing content): **112,035 characters**

The short HTML page was 4.5x larger by character count than the portion of the long markdown page that the summarization model managed to process. The 97-line HTML file was actually about 2x the size of the _entire_ 258K markdown file, just packed into fewer lines.

So line count is the wrong metric entirely. What matters is the total volume of characters (or more precisely, tokens) being sent to the summarization model. A 97-line file full of inline CSS, JavaScript, and icon definitions can easily dwarf a 6,000-line markdown file in terms of what the model actually has to chew through. If you want to gauge whether your page is agent-friendly, look at the character count of the raw response, not the number of lines in your source.

### Wait, Shouldn't the HTML Already Be Gone?

Here's where things get murky. Earlier in this session, Claude told me that the Web Fetch tool "fetches the URL, converts HTML to markdown, then processes the content through a smaller/faster AI model." If that's true, the CSS should have been stripped during the HTML-to-markdown conversion. The summarization model should never have seen `@font-face` declarations and Bootstrap resets. It should have received clean-ish markdown.

But the summarization model clearly _did_ see CSS. It described it in detail. So either Claude's description of how the tool works was wrong, the HTML-to-markdown conversion doesn't strip CSS effectively, or something else entirely is going on. When I pressed Claude on this contradiction, it acknowledged that its description of the tool's internals was speculative rather than authoritative.

After publishing this article, I dug into the question further. Multiple people have [reverse-engineered Claude Code's Web Fetch implementation](https://giuseppegurgone.com/claude-webfetch) and confirmed that it does use the [Turndown](https://github.com/mixmark-io/turndown) library for HTML-to-markdown conversion. So the conversion step is real. But there's a catch.

Turndown has built-in conversion rules for standard markdown elements: paragraphs, headings, lists, links, code blocks, images. When it encounters a tag it doesn't have a rule for, it falls through to a default handler that outputs the element's **text content** as plain text. And `<style>` is not one of its built-in rules. So the CSS rules inside `<style>` tags get dumped directly into the markdown output as raw, unformatted text. The HTML tags get stripped, but the CSS content survives intact.

Turndown _can_ strip style tags. You just have to explicitly tell it to by calling `turndownService.remove('style')`. It's a one-liner. But it's not enabled by default, and the reverse-engineered code appears to show Claude Code using Turndown with default configuration (`Th2.default().turndown(J)` in the deobfuscated source). It's possible there's additional configuration elsewhere in the minified code that wasn't captured, but based on what's visible, there's no evidence that style tags are being explicitly removed.

So nobody was lying, and it's not exactly a bug. The HTML-to-markdown conversion _did_ happen. It just didn't help, because converting `<style>` tag contents to plain text doesn't reduce their size. It just produces unformatted CSS instead of CSS-inside-HTML. For that short page, the CSS came first in the document and consumed the entire truncation window (~100KB), and the real documentation content sitting 87% of the way down never survived the cut.

This is a good reminder that "converts HTML to markdown" can mean very different things depending on the implementation. A naive conversion strips the tags but preserves all the text content, including text that only makes sense as code inside a `<style>` or `<script>` element. A more aggressive conversion would strip those elements entirely. The difference matters enormously when your page has hundreds of kilobytes of inline CSS before the first paragraph of actual content.

### Long Page, HTML-Style

For completeness, I also asked Claude to fetch the HTML version of the long tutorial page. Claude sent the same verbatim-output prompt to the summarization model as before. Same result as the short page: all CSS, no content. But the failure mode was interestingly different.

With the short page, the summarization model appeared to _believe_ that the page was a CSS stylesheet. Its summary concluded with "This stylesheet serves MongoDB's documentation portal, providing comprehensive styling for technical documentation." It treated the CSS as the intended content, not as an obstacle to the real content.

With the long page, the model understood something was wrong. It responded with "I cannot provide the complete page content as requested because the provided text is primarily CSS stylesheet code rather than HTML page content with meaningful text, headings, and sections." It then listed what was missing: headings, paragraphs, links, navigation. It knew it was supposed to be looking at documentation and that the CSS was preventing it from seeing the actual page content.

Same underlying problem, two different failure modes. In one case, the model misidentified what the page was. In the other, the model correctly identified the problem but couldn't do anything about it. Neither case delivered any documentation content to the agent.

## Web Fetch Internals are Not Transparent

If we're documentarians trying to optimize content for agent consumption, we need to understand what happens to our pages between "agent fetches URL" and "model sees content." What are the truncation limits? How does HTML get processed? Is there a summarization step?

So I went looking. The short answer: almost nobody documents this.

### What's Actually Documented

**Claude Code** is the best-documented platform, but mostly thanks to reverse engineering rather than official documentation. Multiple people have dug into the client-side implementation and published their findings. [Giuseppe Gurgone](https://giuseppegurgone.com/claude-webfetch), [Mikhail Shilkov](https://mikhail.io/2025/10/claude-code-web-tools/), and [Liran Yoffe](https://medium.com/@liranyoffe/reverse-engineering-claude-code-web-tools-1409249316c3) have all documented the pipeline in detail:

1. The URL gets validated and normalized (max 2,000 characters, HTTP upgraded to HTTPS).
2. Content is fetched with a max size around 10 MB. The Accept header requests `text/markdown` first, then `text/html`, then anything else.
3. HTML gets converted to markdown using the [Turndown](https://github.com/mixmark-io/turndown) library. As I discuss [above](#wait-shouldnt-the-html-already-be-gone), Turndown strips HTML tags but doesn't necessarily strip the _contents_ of elements like `<style>` and `<script>` unless explicitly configured to do so.
4. The result is truncated to **100 KB of text**.
5. The truncated content goes to a summarization model (Claude 3.5 Haiku) which extracts relevant parts based on whatever prompt the agent sent.

There's also a trusted sites list of roughly 80 hardcoded domains, including `docs.python.org`, `developer.mozilla.org`, `react.dev`, and `learn.microsoft.com`. These get a more generous extraction prompt. And if a trusted site returns `Content-Type: text/markdown` and the content is under 100,000 characters, it bypasses the summarization model entirely. That's a significant detail for documentarians: if your docs site is on that trusted list and you serve markdown, the agent gets your content directly without any intermediate model interpreting it.

On the official side, Anthropic's [API documentation for the web_fetch tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-fetch-tool) documents the configurable parameters (`max_content_tokens`, domain filtering, `max_uses`) but not the internal processing pipeline. Worth noting: the API-level tool and the Claude Code client-side tool are distinct implementations.

**Google Gemini** documents its [URL context tool](https://ai.google.dev/gemini-api/docs/url-context) at a moderate level. It uses a two-stage retrieval: first checking an internal index cache, then falling back to a live fetch. The max content per URL is 34 MB, and you can process up to 20 URLs per request. But how HTML gets converted, whether content is summarized by an intermediate model, and what happens between "content fetched" and "model sees it"? Not documented.

**OpenAI** documents its [web search tool](https://developers.openai.com/api/docs/guides/tools-web-search) with a `search_context_size` parameter (low/medium/high) that controls how much context from search results gets included. The whole thing is limited to a 128K token context window regardless of the underlying model's capacity. But specific truncation thresholds for individual pages, HTML conversion methods, and whether a summarization model is involved? Not documented.

**Windsurf** has a [brief page](https://docs.windsurf.com/windsurf/cascade/web-search) explaining that their system "parses through and chunks up web pages" and "gets only the information that is necessary." For long pages, it "skims to the section we want then reads the text that's relevant." Processing happens locally on your device. That's about all they say.

### What's Not Documented At All

**Cursor**, **GitHub Copilot**, **OpenAI Codex CLI**, and **Devin** provide essentially no public documentation about their web fetch internals. No truncation limits, no processing pipeline details, no information about HTML conversion. If it's out there, I couldn't find it.

### Content Negotiation Tells Part of the Story

One bright spot: [Checkly published a comparison](https://www.checklyhq.com/blog/state-of-ai-agent-content-negotation/) of the Accept headers and User-Agent strings used by major AI agents. This reveals which agents even *ask* for markdown. Only three do: Claude Code, Cursor, and OpenCode. Everyone else requests HTML or uses a generic `*/*` accept header.

This matters because it tells you which agents are even trying to get the lighter-weight version of your content. If an agent doesn't request markdown, it's getting your full HTML page with all the CSS, JavaScript, and boilerplate. Whether the agent then strips that noise internally before the model sees it is... undocumented.

### What This Means for Documentarians

The situation is frustrating. We're being asked to optimize our content for agent consumption, but the platforms won't tell us what the constraints are. Here's what we can piece together from the available information:

- **Truncation is real and varies by platform.** Claude Code truncates between 100-150k of text. Other platforms presumably have limits too, but won't say what they are. If your page is large, some of it is getting cut.
- **Source order matters.** Whatever content appears first in your page source is what the agent is most likely to see. Tabs, accordions, and other UI patterns that flatten into long sequential content in the source mean that later sections may be invisible to agents.
- **Serving markdown helps, but only if the agent requests it.** Most agents don't. And even if they do, you need your server configured to respond appropriately to content negotiation.
- **Character count is the metric that matters, not line count.** As I showed earlier, a 97-line HTML file can be 4.5x larger than the portion of a 6,000-line markdown file that an agent can process.

The lack of transparency here isn't just an inconvenience. It's a real barrier to the "agent-friendly docs" movement. Documentarians can't optimize for constraints they can't see. We're left running experiments, comparing notes, and hoping the platforms eventually decide to tell us what's going on under the hood.
