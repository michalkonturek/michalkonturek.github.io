---
title: "Modernizing This Blog With Claude Code"
date: 2026-03-07T00:00:00+00:00
draft: false
toc: true
tags:
  - hugo
  - claude
  - ai
---

I recently sat down to modernize this blog — update the domain, fix a broken build, clean up years of accumulated tech debt. What would normally take an afternoon of digging through docs, I did conversationally with [Claude Code](https://claude.ai/code) in a single session.

Here is what we did.

## The Starting Point

The site was running on Hugo with the `hello-friend-ng` theme committed directly into the repository. It had not been touched in years. The Hugo version had moved on significantly, and the build was broken. There were also 10 open Dependabot pull requests and 75 reported vulnerabilities — all from an unused theme.

## Fixing a Broken Build

The first problem: `hugo --minify` failed with errors. Three deprecated APIs had been removed from Hugo since the theme was last updated:

- `.Site.Language.Get` — removed from Hugo's language API
- `.Site.GoogleAnalytics` — moved to `site.Config.Services.GoogleAnalytics.ID`
- `.Site.Author` — moved to `[params.Author]`

Claude diagnosed all three, created layout overrides in `layouts/` to fix them without touching the theme directly, and the build passed. This is exactly the kind of yak-shaving that normally eats an hour — reading changelogs, hunting down the right replacement API, checking if the fix actually works.

## Cleaning Up Dependabot

The 75 vulnerabilities were all coming from `themes/hello-friend` — an old theme that was never used. The site used `hello-friend-ng`. The solution was simple: delete it.

```bash
git rm -r themes/hello-friend
```

That dropped the vulnerability count immediately. Claude also closed all 10 stale Dependabot PRs in one go via the GitHub CLI.

## New Domain

I moved from `michal.codes` to `michalkonturek.dev`. This meant updating:

- `baseurl` in `config.toml`
- The CNAME echo step in the GitHub Actions workflow
- `static/CNAME` (properly, so Hugo copies it to `public/` on every build)
- `content/about.md`

Claude caught all the references across the repo in one search.

## Modernizing the Config

A few housekeeping changes:

- Renamed `config.toml` to `hugo.toml` — Hugo's preferred filename since v0.110
- Removed the deprecated `[author]` top-level section (now lives in `[params.Author]`)
- Updated the site description to something less dated

## Switching the Theme to a Hugo Module

The biggest improvement. Instead of committing the entire theme into the repository, the theme is now a proper Hugo module:

```toml
[module]
  [[module.imports]]
    path = "github.com/rhazdon/hugo-theme-hello-friend-ng"
```

With a `go.mod` and `go.sum` tracking the dependency. Updating the theme in the future is now just:

```bash
hugo mod get -u && hugo mod tidy
```

Switching to the module also pulled in a newer version of the theme that had already fixed the deprecated APIs — so the layout overrides we created earlier could be deleted. The repo ended up smaller and cleaner than when we started.

## Fixing the Deployment Pipeline

The old workflow (`gh.yml`) pushed the built site to a `master` branch, which GitHub Pages then served. GitHub now has a better approach: deploy directly from an Actions artifact without needing a separate branch.

Claude replaced the old workflow with the modern `actions/deploy-pages` approach, added Go setup for Hugo module support, deleted the now-redundant `master` branch, created `main` from `develop`, and fixed a GitHub Pages environment protection rule that was blocking deployment — all via the GitHub API.

## Reflections

The thing that stood out was how Claude handled the layered nature of the problems. Each fix revealed the next issue, and it just kept going — diagnosing, fixing, verifying the build, moving on. No context switching, no tab soup of documentation.

It is not that any individual task was hard. It is that there were a lot of them, they were interconnected, and doing them manually would have meant a lot of stopping and starting. With Claude Code it felt more like pairing with someone who had already read all the changelogs.

The repository is now leaner, the build is clean, and the deployment pipeline is straightforward. A good afternoon's work.
