# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal website/portfolio built with Jekyll and hosted on GitHub Pages. It uses the `jekyll/minima` remote theme with a custom `main` layout that overrides the default theme layout.

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

## Architecture

The site uses a **custom layout system** that does not rely on minima's default layouts:

- `_layouts/main.html` — the primary layout used by all pages. Includes Pico CSS (`assets/css/pico.min.css`) and `assets/css/main.css`. Conditionally shows the navbar (hidden on `/`) and switches between `landing-container` and `container` CSS classes based on the current page URL.
- `_layouts/post.html` — layout for blog posts in `_posts/`.
- `_includes/navbar.html`, `header.html`, `google-analytics.html` — partial templates included by the layout.

**Content sources:**

- `_data/projects.json` — drives the portfolio page. Each entry has fields: `title`, `subtitle`, `url`, `thumbnail`, `stack`, `description`, `myRole`, `achievements`, `company`.
- `_posts/` — Markdown blog posts (tech articles on blockchain/Node.js topics).
- `assets/css/main.css` — custom styles on top of Pico CSS.
- `assets/img/` — project thumbnails referenced from `projects.json`.

**Pages:** `index.html`, `about.html`, `portfolio.html`, `dev-notes.html` — all use `layout: main`.

**Deployment:** Pushing to `master` triggers GitHub Pages to build and deploy automatically.
