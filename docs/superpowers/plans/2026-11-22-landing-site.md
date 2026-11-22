# bytebeast.github.io Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Personal landing site with projects, grouped stack grid and a blog, in Catppuccin.

**Architecture:** Jekyll site built natively by GitHub Pages. Content lives in
`_data/*.yml` and `_posts/`, rendered by two layouts and one plain CSS file.
Icons are inline SVG includes so they can be tinted with `currentColor`.

**Tech Stack:** Jekyll via the `github-pages` gem, `jekyll-feed`, `jekyll-seo-tag`, plain CSS.

**Spec:** `docs/superpowers/specs/2026-11-22-landing-site-design.md`

## Global Constraints

- Header name verbatim: `ND c2 MT c2 Nj k2 ZT Zm Mj A0 Nj Y1 Nm M2 OT c4`
- Intro verbatim: "~30 years in Unix/Linux, SRE, DevOps, and infrastructure engineering. It's more than a career; it's been my lifelong passion."
- `<title>`, feed and SEO title: `bytebeast` (builder call: the encoded string is unreadable in a browser tab).
- Only Pages-supported plugins. No JS. No CI workflow.
- No top-level path named `kissaten.nvim`, `check-endpoint`, `winteriscoming.nvim`.
- `docs/` excluded from the build.
- Catppuccin Mocha default, Latte under `prefers-color-scheme: light`. Hex values checked against catppuccin/palette.
- Footer: GitHub link only.

## Review Focus

- 360px-wide viewport: no horizontal scroll, cards and grid wrap.
- Latte mode: every text/background pair used meets WCAG 4.5:1 (Latte yellow/peach/sky do not, so never use them for text).
- Stack item without an icon renders a text badge, not an empty box.
- Long post title wraps cleanly on mobile and in the post list.
- Project links are absolute `https://bytebeast.github.io/<repo>/` so they hit the project Pages sites.

---

### Task 1: Scaffold, layout and palette

**Files:** Create `Gemfile`, `_config.yml`, `.gitignore`, `_layouts/default.html`, `assets/css/style.css`, `index.html`

**Interfaces:**
- Produces: `default` layout with `{{ content }}`, header (encoded name, nav: projects, stack, blog) and footer. CSS custom properties `--base --mantle --crust --surface0..2 --overlay0..2 --text --subtext0..1` plus accents, swapped under the light media query.

- [ ] `git init`, then `bundle config set --local path vendor/bundle && bundle install`. Expected: success. If `github-pages` fails on Ruby 4, add the missing stdlib gems it needs (e.g. `csv`, `logger`, `base64`, `bigdecimal`) to the Gemfile and note it.
- [ ] `bundle exec jekyll build`. Expected: `_site/index.html` contains the encoded name and the intro verbatim, and `_site/docs` does not exist.

### Task 2: Projects

**Files:** Create `_data/projects.yml`, `assets/img/projects/{kissaten,winteriscoming,check-endpoint}.png`; modify `index.html`

**Interfaces:**
- Produces: `site.data.projects[]` with `name`, `url`, `description`, `image`.

- [ ] Images: kissaten `assets/screenshots/evening.png`, winteriscoming `images/screenshots/hero.png` from their Pages sites. check-endpoint has none, so capture it with headless Chrome (already installed). Resize to 1200px wide with `sips`.
- [ ] Descriptions shortened from the repo descriptions.
- [ ] Build. Expected: 3 cards, each image exists and is under 400KB, and the 3 hrefs are exact.

### Task 3: Stack grid

**Files:** Create `_data/stack.yml`, `_includes/icons/<slug>.svg`; modify `index.html`

**Interfaces:**
- Produces: `site.data.stack[]` of `{group, items: [{name, icon?}]}`. Groups and order exactly as in the spec.

- [ ] Fetch the Simple Icons package into `$TMPDIR` and copy the matching slugs. Fall back to Devicon (MIT) for any that are missing, and to a text badge after that. Record which items ended up with badges.
- [ ] Build. Expected: 9 groups, 32 items, every item has either an `<svg>` or `.badge`.

### Task 4: Blog

**Files:** Create `_layouts/post.html`, `blog/index.html`, `_posts/2026-11-22-per-phase-http-timing.md`; modify `index.html`

- [ ] Post: why one latency number hides the phase that is slow (DNS, TCP, TLS, TTFB, transfer, streaming ITL), with `curl -w` timing vars as the example, linking to check-endpoint. No invented check-endpoint flags.
- [ ] Home shows the 3 latest posts. `/blog/` lists all posts.
- [ ] Build. Expected: `_site/blog/index.html` lists the post, `_site/feed.xml` parses as XML and contains the post title.

### Task 5: Verify

- [ ] Add `404.html`.
- [ ] Contrast script (in `$TMPDIR`, not committed) over every text/bg pair used in both flavors. Expected: all >= 4.5:1.
- [ ] Internal link check over `_site`. Expected: no broken hrefs or srcs.
- [ ] Headless Chrome screenshots at 1280px and 360px in both color schemes. Look at them and check the Review Focus items.
