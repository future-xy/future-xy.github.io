# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Jekyll-based academic personal website for Yao Fu (符 尧), built on the [al-folio](https://github.com/alshedivat/al-folio) theme. Hosted at `https://future-xy.github.io` via GitHub Pages. Pushing to `master` auto-deploys via GitHub Actions to the `gh-pages` branch.

## Local development

**Docker (recommended):**
```bash
docker compose pull
docker compose up
# Site available at http://localhost:8080
```

**Native Ruby/Jekyll (macOS):**
```bash
bundle install --with other_plugins
bundle exec jekyll serve --port 4000 --no-watch
# Site available at http://localhost:4000
```
Note: `mini_racer` is Linux-only; Node.js is used as the JS runtime on macOS. The `_config_dev.yml` file (gitignored, create locally) can disable imagemagick to avoid `EINTR` crashes:
```yaml
# _config_dev.yml
imagemagick:
  enabled: false
```
Then run: `bundle exec jekyll serve --config _config.yml,_config_dev.yml`

**Format Liquid/HTML:**
```bash
npx prettier --write .
```

## Key content files

| What to edit | File/dir |
|---|---|
| Site metadata, social links, nav | `_config.yml` |
| Homepage bio and layout | `_pages/about.md` |
| News items | `_news/announcement_N.md` |
| Publications | `_bibliography/papers.bib` |
| CV | `_data/cv.yml` (or `assets/json/resume.json`) |
| Projects | `_projects/` |
| Blog posts | `_posts/YYYY-MM-DD-title.md` |

## Content conventions

**News** (`_news/`): Each file is a Markdown file with frontmatter (`layout: post`, `date`, `inline: true/false`). Inline news renders directly on the about page; non-inline creates a separate page.

**Publications** (`_bibliography/papers.bib`): Standard BibTeX. Add `selected={true}` to show a paper in the "selected publications" section on the homepage. Author names use `<strong>` tags for bold (e.g., `<strong>Fu</strong>, <strong>Yao</strong>`).

**Blog posts** (`_posts/`): Files must be named `YYYY-MM-DD-title.md`. Frontmatter requires `layout: post`, `title`, `date`, `description`. Optional: `tags: [tag1, tag2]`, `categories: [cat]`, `featured: true`. Posts appear at `/blog/YYYY/title/`.

**`_config.yml` changes** require a Jekyll rebuild to take effect. All other content changes hot-reload during local dev.
