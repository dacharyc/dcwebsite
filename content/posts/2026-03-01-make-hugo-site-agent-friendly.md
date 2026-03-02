---
title: Make Your Hugo Site Agent-Friendly
author: Dachary Carey
layout: post
description: In which I show you how I made multiple Hugo sites agent-friendly.
date: 2026-03-01 08:00:00 -0500
url: /2026/03/01/make-hugo-site-agent-friendly/
image: /images/make-your-hugo-site-agent-friendly-hero.jpg
tags: [Documentation]
draft: false
---

With all the writing I've been doing lately around agent-friendly docs, I decided that all new websites I set up will be agent-friendly from the beginning. I designed my two newest websites, the [Agent-Friendly Documentation Spec](https://agentdocsspec.com) and [aeshift](https://aeshift.com), to be agent-friendly from day one. Particularly with the spec, I thought that people _might want to point agents at the spec_, and I wanted to enable that. I pointed an agent at the spec (as local markdown on my filesystem) and built the sites to hit all the agent accessibility criteria. It didn't add that much extra time to setting up each site, and it got even faster after I had a reference implementation I could point the agent at.

For a weekend project, I decided to try retrofitting agent-friendly access patterns onto some existing sites I had. How would it work with a lot of existing content, with a theme I've modified slowly over years? I had two test cases to try:

- The site where you're reading this content, my personal website, which has been a Hugo site since about 2017. It took about an hour in total, mainly because I was doing a lot of additional testing and writing things down along the way.
- The site I wrote for my macOS app, [PR Focus](https://prfocus.app), which uses a more heavyweight theme (Docsy) and has an actual documentation section. It took about 15-20 minutes following what I wrote down from the first set of updates, adapting for the other theme, and doing the testing whose patterns I had already discovered.

I wrote the steps down so you can follow along at home if you want to do something similar.

## Making a personal website agent-friendly

I told Claude about the spec and the `afdocs` tool, pointing it to both resources on my filesystem. It read the spec, ran the tool, and identified the challenges we were starting with:

Results: **3 passed, 1 warning, 4 failed, 13 skipped** (out of 21 checks).

**What passed:**
- `http-status-codes`: Error pages return proper 4xx codes (not soft 404s)
- `cache-header-hygiene`: Cache headers were already reasonable
- `auth-gate-detection`: All pages are publicly accessible

**What failed:**
- `llms-txt-exists`: No llms.txt file existed
- `markdown-url-support`: No pages served markdown at `.md` URLs (0/50 tested)
- `content-negotiation`: Server ignored the `Accept: text/markdown` header entirely
- `content-start-position`: Actual content started 55-98% into the converted HTML on every sampled page (the worst offenders were tag pages, categories, and the contact page at 95-98%)

**What warned:**
- `page-size-html`: Several pages exceeded 50K characters after HTML-to-markdown conversion, with some approaching 100K

**What was skipped:**
- 13 checks were skipped either because they depend on llms.txt existing, markdown being available, or they were not yet implemented. Once we fixed the failures, more of these checks would run and either pass or reveal new issues.

The `content-start-position` failure is worth highlighting. On this site, the theme includes a large header, navigation, author bio, gallery section, and footer on every page. When an agent converts the HTML to markdown, the actual article content doesn't start until more than halfway through the output. For pages with shorter content (like the contact form or tag listings), the content is buried past 90%. This means agents fetching these pages may get mostly navigation and footer content, with the actual content truncated away.

### What We Changed

#### 1. Add Markdown Output Format to Hugo Config

Hugo can generate multiple output formats for each page. We added a custom "Markdown" format that produces an `index.md` file alongside every `index.html`.

In `config.toml`, add these sections:

```toml
[mediaTypes."text/markdown"]
  suffixes = ["md"]

[outputFormats.Markdown]
  mediaType = "text/markdown"
  baseName = "index"
  isPlainText = true
```

Then update the `[outputs]` section to include the new format:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON", "Markdown"]
  page = ["HTML", "Markdown"]
  section = ["HTML", "Markdown"]
```

The `isPlainText = true` setting is important. It tells Hugo not to wrap the output in HTML tags; the template outputs raw text (markdown) directly.

#### 2. Create Markdown Layout Templates

Hugo needs templates for the new Markdown output format. These use the `.md` file extension, which Hugo matches to the Markdown output format automatically.

**`layouts/_default/single.md`** - For individual pages and posts:

```
{{ with .Title }}# {{ . }}{{ end }}
{{ with .Date }}*{{ .Format "January 2, 2006" }}*{{ end }}

{{ .RawContent }}
```

This outputs the page title as an H1 heading and the date in a readable format, then the original markdown content without any HTML rendering, theme chrome, or wrapper markup. Hugo already has your content as markdown (that's what you author in), so the markdown template just passes it through.

**`layouts/_default/section.md`** - For section listing pages (like `/posts/`):

```
{{ with .Title }}# {{ . }}{{ end }}
{{ with .Description }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{ if .Pages }}## Pages
{{ range .Pages }}{{ if not .Draft }}
- [{{ .Title }}]({{ .Permalink }}index.md){{ with .Description }}: {{ . }}{{ end }}
{{ end }}{{ end }}{{ end }}
```

This generates a structured markdown listing with links to the markdown versions of child pages. An agent hitting `/posts/index.md` gets a navigable index of all posts.

**`layouts/index.md`** - For the homepage:

```
{{ with .Title }}# {{ . }}{{ end }}
{{ with .Site.Params.description }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{ if .Site.Menus.main }}## Pages
{{ range .Site.Menus.main }}
- [{{ .Name }}]({{ .URL }}/index.md)
{{ end }}{{ end }}
```

The homepage template lists the site's main navigation as markdown links, giving agents a starting point for discovery.

**`layouts/_default/taxonomy.md`** and **`layouts/_default/term.md`** - For tag/category listing pages:

```
{{ with .Title }}# {{ . }}{{ end }}
{{ .RawContent }}
{{ if .Pages }}## Pages
{{ range .Pages }}{{ if not .Draft }}
- [{{ .Title }}]({{ .Permalink }}index.md){{ with .Description }}: {{ . }}{{ end }}
{{ end }}{{ end }}{{ end }}
```

Hugo distinguishes between `taxonomy` pages (the list of all terms, like `/tags/`) and `term` pages (a specific term, like `/tags/coding/`). Both need templates. The template is the same for both: a title and a list of linked posts. You might wonder whether agents need a markdown version of `/tags/coding/`. The content itself is just a list of links, which isn't very valuable on its own. But the spec recommends content parity checks between HTML and markdown versions, and a naive implementation of that check could flag taxonomy pages without markdown counterparts as failures. Better to have them for completeness.

To enable markdown output for these page kinds, add them to the `[outputs]` config:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON", "Markdown"]
  page = ["HTML", "Markdown"]
  section = ["HTML", "RSS", "Markdown"]
  taxonomy = ["HTML", "RSS", "Markdown"]
  term = ["HTML", "RSS", "Markdown"]
```

The only HTML pages that won't have markdown counterparts after this are Hugo's **pagination pages** (`/page/1`, `/page/2`, etc.). These are a Hugo-internal construct for paginating list views; they don't represent distinct content, and there's no clean markdown analog.

#### 3. Create an llms.txt File

The `llms.txt` file is a machine-readable index of your site's content, following the [llmstxt.org](https://llmstxt.org) proposal. It gives agents a structured starting point: here's what this site is, here are the pages, and here are the URLs to fetch.

We auto-generate `llms.txt` from the content directory so it stays fresh. A shell script runs before each Hugo build, reads the front matter from every content file, and produces the index.

This script is 100% Claude Code - my Bash-foo is not nearly up to this. That said, it's pretty simple, except for the `sed`, which I fully do not grok and didn't try. It seems to work in practice, and my site is a personal blog, so YOLO! You can check out the generated llms.txt at: [https://dacharycarey.com/llms.txt](https://dacharycarey.com/llms.txt)

**`generate_llms_txt`:**

```sh
#!/bin/sh
#
# Generates static/llms.txt from content directory.
# Called by build_and_sync before hugo build.

SITE_DIR="$(cd "$(dirname "$0")" && pwd)"
OUTPUT="$SITE_DIR/static/llms.txt"
BASE_URL="https://your-site.com"

cat > "$OUTPUT" << 'HEADER'
# Your Site Name

> A short description of your site.

## Pages

HEADER

# Add top-level pages
for file in "$SITE_DIR"/content/*.md; do
  [ -f "$file" ] || continue

  title=$(sed -n '/^---$/,/^---$/{ /^title:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
  permalink=$(sed -n '/^---$/,/^---$/{ /^permalink:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )

  [ -z "$permalink" ] && continue

  echo "- [$title](${BASE_URL}${permalink}index.md)" >> "$OUTPUT"
done

cat >> "$OUTPUT" << 'SECTION'

## Posts

SECTION

# Add published posts, sorted by date descending
for file in $(ls -r "$SITE_DIR"/content/posts/*.md 2>/dev/null); do
  [ -f "$file" ] || continue

  # Skip drafts
  draft=$(sed -n '/^---$/,/^---$/{ /^draft:/p; }' "$file" | head -1 | sed 's/.*: *//; s/["\x27]//g')
  [ "$draft" = "true" ] && continue

  title=$(sed -n '/^---$/,/^---$/{ /^title:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
  description=$(sed -n '/^---$/,/^---$/{ /^description:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
  url=$(sed -n '/^---$/,/^---$/{ /^url:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )

  [ -z "$url" ] && continue

  if [ -n "$description" ]; then
    echo "- [$title](${BASE_URL}${url}index.md): $description" >> "$OUTPUT"
  else
    echo "- [$title](${BASE_URL}${url}index.md)" >> "$OUTPUT"
  fi
done
```

Note: this script is written for sites that use flat post files with explicit `url` fields in front matter (e.g., `content/posts/2026-02-18-my-post.md` with `url: /2026/02/18/my-post/`). If your Hugo site uses page bundles (e.g., `content/posts/my-post/index.md`), you'll need to adapt the file discovery and URL extraction. My new site, aeshift.com, uses that format, so it's definitely doable, but my personal blog with this flat structure and explicit `url` fields was more straightforward.

All links in `llms.txt` point to the markdown versions (`index.md`) rather than the HTML pages, so agents can fetch clean content directly.

#### 4. Add Apache .htaccess for Content Negotiation

For sites hosted on Apache, a `.htaccess` file enables two things: content negotiation (so agents can request markdown via the `Accept` header) and proper Content-Type headers for `.md` files.

This Apache is also 100% Claude Code, but it looks plausible and seems to work so - again, personal site, YOLO!

Create **`static/.htaccess`**:

```apache
# Prevent Apache's MultiViews from serving index.html when index.md is requested
Options -MultiViews

RewriteEngine On

# Content negotiation: serve index.md when Accept header prefers text/markdown
RewriteCond %{HTTP_ACCEPT} text/markdown
RewriteCond %{REQUEST_FILENAME} -d
RewriteCond %{REQUEST_FILENAME}/index.md -f
RewriteRule ^(.*)$ $1/index.md [L,T=text/markdown]

RewriteCond %{HTTP_ACCEPT} text/markdown
RewriteCond %{REQUEST_FILENAME}.md -f
RewriteRule ^(.*)$ $1.md [L,T=text/markdown]

# Serve .md files with the correct content type
AddType text/markdown .md

# Cache headers for agent-facing resources
<FilesMatch "\.md$">
    Header set Cache-Control "max-age=3600, must-revalidate"
</FilesMatch>

<Files "llms.txt">
    Header set Cache-Control "max-age=3600, must-revalidate"
</Files>
```

Key details:

- **`Options -MultiViews`** is critical. Without it, Apache's content negotiation may serve `index.html` when you request `index.md`, because MultiViews considers both files as variants of "index" and may prefer the HTML version. This was an actual gotcha caught during testing.
- The **rewrite rules** handle two cases: requests to directories (serve `index.md` from within) and requests to files (serve the `.md` variant).
- The **1-hour cache** (`max-age=3600`) balances freshness with performance. Agents re-fetching within an hour get cached content; after an hour, they revalidate. This is especially important for `llms.txt`, which agents may fetch frequently as a discovery starting point.

If you're not on Apache, you'll need equivalent configuration for your server (nginx `location` blocks, Caddy directives, Cloudflare rules, etc.).

#### 5. Add an Agent Discovery Directive

The agent discovery directive is a hidden HTML element that tells agents where to find the markdown versions and the `llms.txt` index. It's invisible to human visitors but readable by agents that convert HTML to markdown.

Create **`layouts/partials/llms-directive.html`**:

```html
<div class="sr-only" style="position:absolute;width:1px;height:1px;padding:0;margin:-1px;overflow:hidden;clip:rect(0,0,0,0);white-space:nowrap;border:0">
  For AI agents: a documentation index is available at /llms.txt — markdown
  versions of all pages are available by appending index.md to any URL path.
</div>
```

This uses the standard `sr-only` (screen reader only) CSS pattern. The text is in the DOM and will be included when agents convert the page to markdown, but it's visually hidden from humans.

Include this partial in your base template so it appears on every page. We overrode the theme's `baseof.html` to add it inside `<main>`:

```html
<main class="content" aria-label="Content">
  {{ partial "llms-directive.html" . }}
  {{ block "main" . }}{{ end }}
</main>
```

By placing it at the top of `<main>`, the directive appears early in the converted markdown output, before the actual page content. This means even if the page is truncated, the agent will have seen the directive and knows where to find cleaner content.

#### 6. Update the Build Script

I use a build-and-sync script to SSH files onto my web host's server. I added the `generate_llms_txt` call before the Hugo command that generates the static HTML, so the index is always current:

```sh
#!/bin/sh
./generate_llms_txt
hugo
rsync -avz public/ your-server:your-site.com/
```

### Verifying You Haven't Broken Anything

Before deploying, it's worth verifying that the HTML output hasn't changed in unexpected ways. The changes above should only affect the HTML in one place: the addition of the `llms-directive` div. Everything else (new markdown files, llms.txt, .htaccess) is additive.

A simple way to check: curl a few pages from your live site, build locally, and diff them. Pick a representative mix of page types (homepage, a blog post, a standalone page, a section listing, a tag page).

```sh
# Fetch live pages
curl -s https://your-site.com/ -o /tmp/live-home.html
curl -s https://your-site.com/some-post/ -o /tmp/live-post.html
curl -s https://your-site.com/about/ -o /tmp/live-about.html
curl -s https://your-site.com/posts/ -o /tmp/live-posts.html
curl -s https://your-site.com/tags/some-tag/ -o /tmp/live-tag.html

# Build locally
hugo

# Diff each pair
diff /tmp/live-home.html public/index.html
diff /tmp/live-post.html public/some-post/index.html
# ... etc.
```

You should see exactly two differences per page:

1. The `llms-directive` div added inside `<main>` (5 lines, intentional)
2. A possible trailing newline at end of file (cosmetic)

If you see anything else, investigate before deploying. One gotcha we caught this way: when you explicitly define `[outputs]` in your Hugo config, you override Hugo's defaults for each kind. Hugo's default for `section` includes RSS output, so if you write `section = ["HTML", "Markdown"]` without `"RSS"`, your section pages lose their `<link rel="alternate" type="application/rss+xml" ...>` tag. The fix is straightforward: include `"RSS"` in the section outputs:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON", "Markdown"]
  page = ["HTML", "Markdown"]
  section = ["HTML", "RSS", "Markdown"]
```

This is easy to miss because Hugo's defaults are implicit. Once you start explicitly listing outputs for a kind, you take full ownership of that list. Check [Hugo's default output formats](https://gohugo.io/configuration/output-formats/) to see what each kind normally includes.

#### Check for missing markdown counterparts

After a clean build (`rm -rf public && hugo`), verify that every HTML page has a markdown counterpart:

```sh
find public -name "index.html" | while read html; do
  dir=$(dirname "$html")
  if [ ! -f "$dir/index.md" ]; then echo "$dir"; fi
done | sort
```

If this produces output, you have HTML pages without markdown versions. Common cases:

- **Pagination pages** (`/page/1`, `/page/2`, etc.): These are Hugo-internal. They don't represent distinct content and don't need markdown versions.
- **Taxonomy pages** (`/tags/`, `/tags/coding/`, `/categories/`): If you see these, you forgot to add `taxonomy` and `term` to your `[outputs]` config, or you're missing the `taxonomy.md`/`term.md` templates.
- **Stale artifacts**: If you see unexpected pages (old URLs, paths that don't match your content), try a clean build. Hugo's `public/` directory is additive by default; it doesn't remove files from previous builds. Always `rm -rf public` before a verification build.

#### Check for theme demo content leaking into your output

Many Hugo themes ship with example content in their `themes/<name>/content/` directory: placeholder posts, style guide pages, and so on. Hugo merges theme content with your site content at build time. If the theme's example posts are marked `draft: true`, Hugo won't render HTML for them, but the raw source files can still end up in your build output, especially if they use alternative markdown extensions like `.markdown` instead of `.md`.

Check for this after a build:

```sh
# Look for theme content files copied into output
find public -name "*.markdown"

# Check if the theme has a content directory
ls themes/*/content/
```

If you find files, the fix is simple: delete the theme's example content directory. Your own `content/` files override the theme's versions for pages like `about.md` and `contact.md`, so removing the theme's content won't break anything. But demo posts and style guide pages that only exist in the theme will keep showing up in your build output until you remove them.

This matters for agent-friendliness because those raw source files (complete with front matter) are being served publicly. An agent fetching one of these URLs gets lorem ipsum with YAML front matter, which could confuse it about what your site actually contains.

#### Validate llms.txt links

Check that every URL in `llms.txt` points to a file that actually exists in the build output:

```sh
grep -oE 'https://your-site\.com[^ )]+index\.md' static/llms.txt | while read url; do
  local_path="public${url#https://your-site.com}"
  if [ ! -f "$local_path" ]; then echo "MISSING: $url"; fi
done
```

#### Check for empty markdown files

Make sure no markdown files are unexpectedly empty:

```sh
find public -name "index.md" -empty
```

#### Check markdown size distribution

A quick size survey of the generated markdown files can catch problems that other checks miss. If a post is 10K of markdown source but the generated `index.md` is only 50 bytes, something went wrong with the template.

```sh
# Overview stats
find public -name "index.md" -exec wc -c {} \; | sort -n | awk '
  {sizes[NR]=$1; files[NR]=$2}
  END {
    print "Smallest: " sizes[1] " bytes - " files[1]
    print "Largest:  " sizes[NR] " bytes - " files[NR]
    print "Total files: " NR
    sum=0; for(i=1;i<=NR;i++) sum+=sizes[i]
    print "Average: " int(sum/NR) " bytes"
  }'

# Flag anything suspiciously small (under 100 bytes)
find public -name "index.md" -exec sh -c \
  'size=$(wc -c < "$1"); if [ "$size" -lt 100 ]; then echo "$size bytes: $1"; fi' _ {} \;
```

Some small files are expected. A taxonomy page for a category you don't use, or a section with no published pages, might only contain a heading. But if an article you know is substantial shows up at 0 or near-0 bytes, your `single.md` template isn't rendering for that page kind. This is how you'd catch a mismatch between the `layout` field in a post's front matter and the template Hugo selects for the markdown output format.

#### Verify other outputs aren't affected

Confirm that JSON search index and RSS feeds still generate:

```sh
ls -la public/index.json          # Search index
find public -name "index.xml"     # RSS feeds
```

### Results After Remediation

After deploying all changes, we re-ran the scan:

Results: **11 passed, 3 warnings, 0 failed, 7 skipped** (out of 21 checks).

| | Before | After |
|---|---|---|
| Passed | 3 | **11** |
| Warnings | 1 | **3** |
| Failed | **4** | **0** |
| Skipped | 13 | 7 |

Every previously-failing check now passes:

- `llms-txt-exists`: Found at `/llms.txt`
- `llms-txt-valid`: Follows the proposed structure (H1, blockquote, heading-delimited link sections)
- `llms-txt-size`: 9,955 characters (well under the 50K threshold)
- `llms-txt-links-resolve`: All 56 links resolve
- `llms-txt-links-markdown`: 100% of links point to markdown content
- `markdown-url-support`: 50/50 sampled pages support `.md` URLs
- `content-negotiation`: 50/50 sampled pages support `Accept: text/markdown`
- `content-start-position`: Content starts within the first 10% on all sampled pages (median 0%)

That last one is a dramatic improvement. Before, content started 55-98% into every page. Now the median is 0% because agents are fetching the markdown versions directly, which contain nothing but content.

The 3 remaining warnings are all benign:

1. **`page-size-markdown`**: One long article (54K characters) slightly exceeds the 50K pass threshold. This is just a long post; the content itself is fine.
2. **`page-size-html`**: Same page, same reason. The "0% boilerplate" in the report confirms the HTML check is now fetching the markdown URL too.
3. **`markdown-code-fence-validity`**: A post with nested code fences using different delimiter types (backticks wrapping tildes) triggers a false positive. This is [valid CommonMark](https://spec.commonmark.org/0.31.2/#fenced-code-blocks); we filed a bug against the checker. Since noting this, I've already fixed the bug.

The 7 skipped checks are all marked "Not yet implemented" in the tool.

If you want to see the PR with all the changes, it's here: [Make the website agent-friendly](https://github.com/dacharyc/dcwebsite/pull/26)

Most of it is actually removing the theme content files that were auto-generating content in my build.

### Size Comparison: Before and After

To illustrate why markdown output matters, here's a comparison for a single article ("Agent-Friendly Docs"):

| Version | Size |
|---------|------|
| HTML page | ~105K characters |
| Markdown version | ~35K characters |

The HTML version includes the full theme: navigation, author bio, social links, gallery, footer, related posts, share buttons, and all the CSS class markup. The markdown version is just the article content. An agent fetching the markdown version gets 100% useful content with zero waste.

## Adapting for Your Site

The specific changes depend on your Hugo setup:

- **Theme differences**: The llms-directive partial uses inline styles that work universally regardless of theme. If your theme has a `sr-only` utility class, you can use that instead of inline styles.
- **Content structure**: If you use page bundles (`content/posts/my-post/index.md`) instead of flat files (`content/posts/2026-02-18-my-post.md`), adapt the `generate_llms_txt` script accordingly. The directory traversal and URL extraction will differ.
- **Front matter fields**: The script looks for `url` and `permalink` fields. If your site derives URLs from filenames or uses a different field, adjust the extraction logic.
- **Server configuration**: The `.htaccess` rules are Apache-specific. Nginx, Caddy, and other servers need equivalent configuration for content negotiation and Content-Type headers.
- **Existing `.htaccess`**: If you already have a `.htaccess` with rewrite rules, merge carefully. The `RewriteEngine On` directive only needs to appear once.

## Docsy Theme: Additional Considerations

We applied the same pattern to a second Hugo site using the [Docsy](https://www.docsy.dev/) theme, which is popular for documentation sites. Docsy introduces several complications that a simpler blog theme doesn't have. But with the prior patterns to follow, Claude was able to complete the updates, including testing, in about 15-20 minutes.

### Multiple baseof.html templates

Docsy uses separate `baseof.html` files for different content sections. The theme ships with `layouts/docs/baseof.html` and `layouts/blog/baseof.html` (or `news/baseof.html` if you've customized it) in addition to the standard `layouts/_default/baseof.html`. Hugo's template lookup order means a section-specific baseof always wins over the default.

If you only override `layouts/_default/baseof.html` to add the agent directive, your docs pages won't get it. You need to override every baseof that your site actually uses. Check which ones your theme provides:

```sh
find themes/*/layouts -name "baseof.html"
```

Then create project-level overrides for each one, adding `{{ partial "llms-directive.html" . }}` to each. Copy the theme's version and add the partial; don't start from scratch, because section-specific baseof templates often include theme features (version banners, sidebar navigation) that you don't want to lose.

### Preserving existing output formats

Docsy configures a "print" output format for section pages, which generates printer-friendly versions. When you add your `[outputs]` section to config.toml, you override Hugo's defaults entirely. If you don't include `"print"` in the section outputs, those printer-friendly pages disappear.

Check your theme's config for existing custom output formats before writing your own:

```sh
grep -A 5 '\[outputs\]' themes/*/config.toml themes/*/hugo.toml 2>/dev/null
```

Then merge them with the markdown format:

```toml
[outputs]
  home = ["HTML", "RSS", "Markdown"]
  section = ["HTML", "print", "RSS", "Markdown"]
  page = ["HTML", "Markdown"]
  taxonomy = ["HTML", "RSS", "Markdown"]
  term = ["HTML", "RSS", "Markdown"]
```

### Multilingual content directories

Docsy's default setup uses `content/en/` rather than `content/` for English-language content (with `content/fr/`, `content/ja/`, etc. for other languages). Your `generate_llms_txt` script needs to point at the right directory:

```sh
CONTENT_DIR="$SITE_DIR/content/en"
```

### Hierarchical documentation structure

A Docsy docs site typically has nested documentation sections several levels deep: `docs/getting-started/installation/`, `docs/configuration/advanced/`, etc. The `generate_llms_txt` script needs to recursively walk the docs tree using `find` rather than globbing a flat directory. It also needs to derive URLs from the directory structure, since docs pages generally don't have explicit `url` fields in front matter.

```sh
find "$CONTENT_DIR/docs" -name "*.md" | sort | while read file; do
  # Skip drafts
  draft=$(sed -n '/^---$/,/^---$/{ /^draft:/p; }' "$file" | head -1 | sed 's/.*: *//; s/["'"'"']//g')
  [ "$draft" = "true" ] && continue

  title=$(sed -n '/^---$/,/^---$/{ /^title:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
  [ -z "$title" ] && continue

  # Derive URL from file path relative to content dir
  relpath="${file#$CONTENT_DIR/}"
  case "$relpath" in
    */_index.md) url_path=$(dirname "$relpath") ;;
    *.md) url_path=$(echo "$relpath" | sed 's/\.md$//') ;;
  esac

  echo "- [$title](${BASE_URL}/${url_path}/index.md)" >> "$OUTPUT"
done
```

### YAML folded scalar descriptions

Some Docsy docs use YAML folded scalars for the `description` field:

```yaml
description: >
  This is a long description that spans
  multiple lines in the source.
```

A naive `sed` extraction pulls out just `>` as the description value. Handle this by detecting the scalar indicator and reading the next indented line:

```sh
desc_line=$(sed -n '/^---$/,/^---$/{ /^description:/p; }' "$file" | head -1)
description=$(echo "$desc_line" | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
if [ "$description" = ">" ] || [ "$description" = "|" ]; then
  description=$(sed -n '/^description:/{ n; s/^  *//; p; }' "$file" | head -1)
fi
```

### Permalink slugs for blog/news posts

Hugo's `:slug` permalink variable defaults to the slugified page title, not the filename. If your Docsy site has news posts at `content/en/news/2024/1.1.md` with a permalink pattern like `/:section/:year/:month/:day/:slug/`, the slug comes from the title ("Introducing Tags and Filtering in PR Focus!"), not from the filename ("1.1"). This means the generated URL in `llms.txt` won't match the actual URL Hugo produces.

The fix: add an explicit `slug` field to each post's front matter:

```yaml
---
title: Introducing Tags and Filtering in PR Focus!
slug: introducing-tags-and-filtering-in-pr-focus
date: 2024-08-12
---
```

Then extract the slug in your `generate_llms_txt` script, falling back to the filename if no slug is specified:

```sh
slug=$(sed -n '/^---$/,/^---$/{ /^slug:/p; }' "$file" | head -1 | sed "s/.*: *['\"]\\{0,1\\}//; s/['\"]\\{0,1\\}$//" )
[ -z "$slug" ] && slug=$(basename "$file" .md)
```

### Shortcodes in markdown output

Hugo's `{{ .RawContent }}` template function outputs the original markdown source, which means Hugo shortcodes appear unprocessed. A docs page that uses `{{</* ref "docs/getting-started/" */>}}` to create internal links will have that shortcode appear literally in the markdown output instead of the resolved URL.

This is a known limitation. The alternative, `{{ .Content }}`, outputs fully rendered HTML, which defeats the purpose of the markdown output format. In practice, agents can still read and understand the content; the shortcode syntax is clear enough that both humans and agents can figure out where the link was pointing. If this is a dealbreaker for your site, you could use `{{ .Content }}` and accept the HTML output, or post-process the markdown to resolve shortcodes with a custom template.

### Fix your baseURL

Hugo's `{{ .Permalink }}` template function, which the section and term markdown templates use to build links to child pages, derives its output from the `baseURL` in your config. If your baseURL uses `http://` instead of `https://`, or has unexpected casing (like `PRFocus.app` instead of `prfocus.app`), every link in your generated markdown will inherit that.

The `llms.txt` generator script won't catch this because it uses a hardcoded `BASE_URL` variable. So you can end up with `llms.txt` linking to `https://prfocus.app/docs/index.md` while `docs/index.md` links to `http://PRFocus.app/docs/overview/index.md`. The links still work because of HTTPS redirects and case-insensitive DNS, but it's inconsistent and looks sloppy to an agent parsing URLs.

The fix is simple: make sure your `baseURL` uses the canonical form of your domain:

```toml
# Before
baseURL = 'http://PRFocus.app/'

# After
baseURL = 'https://prfocus.app/'
```

After changing it, do a clean rebuild and verify that all URLs are consistent across `llms.txt`, the section markdown files, the HTML output, and RSS feeds:

```sh
# Check for any remaining http:// or wrong-case URLs
grep -r 'http://YourOldDomain' public/ --include="*.md"
grep -r 'http://YourOldDomain' public/ --include="*.xml"
```

This is easy to miss because many Hugo sites have been running with a slightly wrong baseURL for years without issue. Browsers handle the redirect transparently, so nobody notices. But when you're generating machine-readable markdown files full of absolute URLs, consistency matters.

### Docsy results

After applying all the changes above to the Docsy-based site, the scan results were:

Results: **13 passed, 1 warning, 0 failed, 7 skipped** (out of 21 checks).

Every check that the tool currently implements passes. The single warning is `content-start-position`: one page out of 31 has content starting at 11% into the converted output. This is the homepage, where Docsy's landing page layout has some structural HTML before the main content. For all other pages, content starts at 0% because agents fetch the markdown versions directly.

The Docsy site required more work than the simpler blog theme. Beyond the standard changes (config, templates, llms.txt, .htaccess, directive), we needed to:

- Override three separate `baseof.html` files (default, docs, news)
- Preserve the existing "print" output format in the section outputs
- Point the `generate_llms_txt` script at `content/en/` instead of `content/`
- Use recursive `find` for hierarchical docs instead of flat directory globbing
- Handle YAML folded scalar descriptions in front matter
- Add explicit `slug` fields to news posts to match Hugo's permalink output
- Fix the `baseURL` from `http://PRFocus.app/` to `https://prfocus.app/`

None of these are difficult individually, but they add up. The core pattern (markdown output format, llms.txt, .htaccess, directive) is the same across themes. What changes is the plumbing around it, especially around template lookup order, existing output formats, content directory structure, and URL generation.

## File Summary

Here's everything we created or modified:

| File | Type | Purpose |
|------|------|---------|
| `config.toml` | Modified | Added markdown output format and updated outputs |
| `build_and_sync` | Modified | Added `./generate_llms_txt` before `hugo` |
| `generate_llms_txt` | New | Shell script to auto-generate `static/llms.txt` from content front matter |
| `static/.htaccess` | New | Apache content negotiation, Content-Type, and cache headers |
| `static/llms.txt` | New (generated) | Machine-readable site index for agent discovery |
| `layouts/_default/baseof.html` | New (override) | Theme base template override to include agent directive |
| `layouts/_default/single.md` | New | Markdown output template for individual pages |
| `layouts/_default/section.md` | New | Markdown output template for section listings |
| `layouts/_default/taxonomy.md` | New | Markdown output template for taxonomy list pages |
| `layouts/_default/term.md` | New | Markdown output template for taxonomy term pages |
| `layouts/index.md` | New | Markdown output template for the homepage |
| `layouts/partials/llms-directive.html` | New | Hidden agent discovery directive |
