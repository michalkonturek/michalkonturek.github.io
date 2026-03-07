# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Hugo static site (personal blog at michal.codes). Content is written in Markdown, styled with the `hello-friend-ng` theme (included as a git submodule).

## Commands

```bash
# Start local dev server with live reload
hugo server

# Build for production
hugo --minify

# Create a new post
hugo new posts/my-post-title.md
```

## Architecture

- `config.toml` — site configuration: theme, language, social links, nav menus
- `content/posts/` — blog posts as Markdown files with YAML/TOML front matter
- `content/about.md` — the About page
- `archetypes/default.md` — template used when creating new content with `hugo new`
- `static/` — static assets (images, etc.) served at the root URL
- `themes/hello-friend-ng/` — Hugo theme (git submodule); avoid modifying directly
- `resources/_gen/` — auto-generated asset cache (do not edit)

## Deployment

Pushing to the `develop` branch triggers GitHub Actions (`.github/workflows/gh.yml`), which builds the site with `hugo --minify` and deploys the `public/` directory to the `master` branch (served via GitHub Pages at michal.codes).

## Content Front Matter

Posts support these front matter fields (from the theme archetype):

```yaml
title: "Post Title"
date: 2024-01-01T00:00:00+00:00
draft: true        # set to false to publish
toc: false         # table of contents
images:
tags:
  - untagged
```
