# michalkonturek.github.io

Source for [michalkonturek.dev](https://michalkonturek.dev) — personal blog built with [Hugo](https://gohugo.io) and the [hello-friend-ng](https://github.com/rhazdon/hugo-theme-hello-friend-ng) theme.

## Development

```bash
# Install Hugo (macOS)
brew install hugo

# Start local dev server with live reload
hugo server

# Build for production
hugo --minify

# Create a new post
hugo new posts/my-post-title.md

# Update theme
hugo mod get -u && hugo mod tidy
```

## Structure

```
content/posts/   # Blog posts (Markdown)
content/about.md # About page
static/          # Static assets served at root (images, CNAME)
layouts/         # Theme overrides
hugo.toml        # Site configuration
go.mod / go.sum  # Hugo module dependencies (theme)
```

## Deployment

Pushing to `main` triggers GitHub Actions (`.github/workflows/hugo.yml`), which builds the site and deploys it to GitHub Pages at [michalkonturek.dev](https://michalkonturek.dev).
