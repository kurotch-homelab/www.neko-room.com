# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page Hugo static site that serves as Katsunori Kuroda's resume at https://www.neko-room.com/ (custom domain via `CNAME`, hosted on GitHub Pages). There is no `content/` directory — every piece of resume data is configured in `hugo.toml`, and rendering is delegated to the `devresume` theme.

## Common commands

```bash
# Local preview (Hugo extended required; CI uses v0.128.0)
hugo server -D

# Production build (matches CI)
hugo --minify

# Pull the theme submodule after cloning
git submodule update --init --recursive
```

There are no tests, linters, or Node build steps. The `package-lock.json` step in CI is a no-op for this repo.

## Architecture

- **`hugo.toml`** is the source of truth. Editing the resume (skills, education, projects, contact, social, etc.) means editing the `[params.*]` tables here — not creating Markdown content. The `devresume` theme reads these params directly.
- **`themes/devresume/`** is a **git submodule** (`https://github.com/cowboysmall-tools/hugo-devresume-theme`). Do not edit files inside it; overrides go in the top-level `layouts/` directory using Hugo's lookup order.
- **`layouts/partials/scripts.html`** is the one override currently in place — it injects FontAwesome and the Google Analytics tag (`G-VRZ0780Q3X`), shadowing the theme's partial of the same name. Add further per-site overrides the same way (mirror the theme's path under `layouts/`).
- **`static/assets/images/`** holds the avatar and any other static images referenced from params (e.g. `avatar.png` in `[params.profile]`).
- **`archetypes/default.md`** exists but is unused in practice since the site has no posts.

## Deployment

`.github/workflows/hugo.yml` builds with Hugo extended 0.128.0 + Dart Sass and deploys to GitHub Pages on every push to `main` (also `workflow_dispatch`). The workflow checks out submodules recursively and overrides `--baseURL` with the Pages base URL, so `baseURL` in `hugo.toml` only matters for local builds. `CNAME` keeps the custom domain bound on each deploy.
