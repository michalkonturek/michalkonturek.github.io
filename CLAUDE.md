# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Hugo static site (personal blog at michalkonturek.dev). Content is written in Markdown, styled with the `hello-friend-ng` theme managed as a Hugo module.

## Commands

```bash
# Start local dev server with live reload
hugo server

# Build for production
hugo --minify

# Create a new post
hugo new posts/my-post-title.md

# Update theme
hugo mod get -u && hugo mod tidy
```

## Architecture

- `hugo.toml` — site configuration: theme, language, social links, nav menus
- `go.mod` / `go.sum` — Hugo module dependencies (theme is `github.com/rhazdon/hugo-theme-hello-friend-ng`)
- `content/posts/` — blog posts as Markdown files with YAML/TOML front matter
- `content/about.md` — the About page
- `archetypes/default.md` — template used when creating new content with `hugo new`
- `static/` — static assets (images, CNAME, etc.) served at the root URL
- `resources/_gen/` — auto-generated asset cache (do not edit)

## Deployment

Pushing to the `main` branch triggers GitHub Actions (`.github/workflows/hugo.yml`), which builds the site with Hugo and deploys via the GitHub Pages artifact mechanism (not branch-based). The site is served at michalkonturek.dev.

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
