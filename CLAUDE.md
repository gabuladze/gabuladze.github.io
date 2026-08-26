# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal website/portfolio built with Jekyll and hosted on GitHub Pages at `abula.dev`. It uses a custom `main` layout and Pico CSS; it does **not** use a Jekyll theme's layouts.

## Commands

```bash
# Install dependencies
bundle install

# Serve locally with live reload
bundle exec jekyll serve

# Build the site
bundle exec jekyll build
```

Note: `_config.yml` is NOT reloaded automatically during `jekyll serve` — restart the server after any config changes.

`_config.yml` sets `repository:` explicitly because this repo's git remote is named `github`, not `origin`. Without it, `jekyll-github-metadata` fails the build locally with "No repo name found."

## Architecture

- `_layouts/main.html` — the only layout. Every page uses it. Includes Pico CSS (`assets/css/pico.min.css`) and `assets/css/main.css`. Conditionally shows the navbar (hidden on `/`) and switches between `landing-container` and `container` CSS classes based on the current page URL.
- `_includes/navbar.html`, `google-analytics.html` — partial templates included by the layout. The GA measurement ID is hardcoded in the include, not read from `_config.yml`.
- The layout emits no `<title>` of its own. `{% seo %}` writes it, which is what makes the per-page `title:` front matter take effect. Adding a second `<title>` silently wins over the seo one.

**Content sources:**

- `work.html` and `portfolio.html` — **both deliberately hand-written HTML, not data-driven.** There is no `_data/` directory. A Liquid loop over a data file forces every entry through one template, which produces a uniform, machine-sounding rhythm; `work.html` varies its prose structure on purpose. Do not convert either page to a data file.
- The two pages split the work: `work.html` carries the reasoning, `portfolio.html` is an index of things a visitor can click. A portfolio block is a thumbnail, an `<h3>`, one `item-lede` subtitle and the link — no description and no stack tags, because that material is on `work.html` and repeating it made the portfolio page contradict its own lede.
- `assets/css/main.css` — custom styles on top of Pico CSS.
- `assets/img/` — project thumbnails, referenced directly from `portfolio.html`. Only projects with a block on that page have an image; the "Earlier work" items on `work.html` are text links by design.

**Pages:** `index.html`, `about.html`, `work.html`, `portfolio.html`, `404.html` — all use `layout: main`.

There is no blog. `_posts/`, `dev-notes.html` and `_layouts/post.html` were removed deliberately; the five old post URLs now 404. `jekyll-feed` is still in the `github-pages` gem's default plugin list, so the deployed site emits an empty `/feed.xml`. Nothing links to it.

`_config.yml` sets `theme: null`, and it is load-bearing. With no `theme` key the `github-pages` gem defaults to `jekyll-theme-primer`, which builds a 136 KB `assets/css/style.css` into the site that nothing links to.

Removing the theme also removes how `{% seo %}` used to load: locally, Jekyll requires a theme gem's `jekyll-*` runtime dependencies, and `jekyll-theme-primer` depends on `jekyll-seo-tag`. The site was getting its `<title>` and meta tags from a theme nobody chose. That is why `plugins:` now names `jekyll-seo-tag` explicitly — delete that line and the local build dies with "Unknown tag 'seo'".

**Local and deployed builds no longer load the same plugins.** Locally you get exactly what `plugins:` lists. On GitHub Pages the gem unions that list with its own defaults, so `jekyll-feed`, `jekyll-optional-front-matter` and `jekyll-github-metadata` all still run there. The five real pages render identically either way; the difference shows up only as a stray `/feed.xml` and in how loose `.md` files are treated.

The `exclude:` list is load-bearing for that last reason: GitHub Pages auto-loads `jekyll-optional-front-matter`, which will publish any stray root `.md` file (including this one) as a live page unless excluded.

## Writing standards for site copy

Site copy follows a specific voice. Before editing any user-facing text, read the plan at `~/.claude/plans/` if present, or follow these rules:

- Short sentences, plain English, active voice. Lead with what was built, not what was maintained.
- At most one em dash per page. Vary sentence and paragraph length. No three-item lists as a default rhythm. Never repeat a `**claim** — explanation` template down a list.
- Banned phrases: "end to end", "leveraged", "passionate about", "proven track record", "rapid learner", "solving complex challenges".
- Keep crypto jargon but explain it inline. A backend engineer who has never touched a blockchain should be able to follow every sentence.
- "nodes" is not a synonym for "validators". The chain has more nodes than validators.
- Never state a hard fork count, the absolute value of the bridge fee saving, or which database backs any indexer.
- Kubernetes is used via `kubectl` and OpenLens to inspect and debug running services. No cluster setup. Never write a bare `k8s` in a stack line.

**Deployment:** Pushing to `master` triggers GitHub Pages to build and deploy automatically.
