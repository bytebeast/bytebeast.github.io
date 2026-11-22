# bytebeast.github.io landing site

## Intent

Personal site at `https://bytebeast.github.io/`. Hiring managers are the main
visitors (they arrive via the GitHub link on the resume), but the site is not a
hiring page. It should read as a personal site that holds up well under that
audience.

## Goals

- Header shows the GitHub profile name exactly as set (encoded, not decoded).
- Intro is the GitHub bio, lightly edited:
  "~30 years in Unix/Linux, SRE, DevOps, and infrastructure engineering. It's
  more than a career; it's been my lifelong passion."
- Projects first: kissaten.nvim, winteriscoming.nvim, check-endpoint as cards
  with screenshots, linking to their Pages sites.
- Grouped stack grid with icons, no skill bars:
  - Cloud & IaC: AWS, Amazon RDS, Terraform, Terragrunt
  - Containers: Kubernetes, Helm
  - Linux/Unix: Solaris, Red Hat, CentOS, AlmaLinux, Ubuntu, Amazon Linux
  - Languages: Go, Python, Java, Bash
  - Data & streaming: MySQL, Redis, Elasticsearch, Kafka, ZooKeeper
  - Networking & security: Nginx, Apache, HAProxy, Check Point
  - Observability: Prometheus, Grafana
  - CI/CD: Jenkins, Harness
  - Editors & AI: Vim, Neovim, Claude
- Blog with one technical sample post (per-phase HTTP timing, links to
  check-endpoint) and an RSS feed.
- Catppuccin Mocha by default, Latte via `prefers-color-scheme: light`.

## Non-goals

Resume, LinkedIn, analytics, JS framework, comments, theme toggle.

## Constraints

- Public repo named `bytebeast.github.io`.
- No top-level paths named `kissaten.nvim`, `check-endpoint` or
  `winteriscoming.nvim`; project Pages sites own those paths.
- Jekyll built natively by GitHub Pages, only Pages-supported plugins
  (`jekyll-feed`, `jekyll-seo-tag`). No CI workflow.

## Approaches

1. Jekyll built by Pages (chosen). Markdown posts, zero CI, RSS via
   `jekyll-feed`. Local preview needs a modern Ruby.
2. Hugo plus a GitHub Actions deploy. Fast single binary, but a workflow to keep
   pinned and zizmor-clean.
3. Plain HTML/CSS. Nothing to build, but posts and RSS are hand-maintained.

## Builder calls

- Icon SVGs vendored into the repo (Simple Icons, CC0). Text badge where no
  icon exists.
- Screenshots from each project's social preview image if present; otherwise
  ask before installing a headless browser.
- Footer has a GitHub link only.
- `docs/` excluded from the Jekyll build.
