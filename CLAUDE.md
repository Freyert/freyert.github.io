---
layout: default
title: CLAUDE.md
nav_exclude: true
---

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Setup and Development Commands

This is a Jekyll-based personal website hosted on GitHub Pages. Before running any commands, install dependencies:

```bash
bundle install
```

### Build and Development
- **Local development server**: `bundle exec jekyll serve` (serves at http://localhost:4000)
- **Build for production**: `bundle exec jekyll build` (outputs to `_site/`)
- **Development with live reload**: `bundle exec jekyll serve --livereload`
- When running the Jekyll server, always run it in the background with `&` to free up the terminal

### Dependencies
- Uses `github-pages` gem for GitHub Pages compatibility
- Uses `hawkins` gem for enhanced development server features
- Requires `webrick` for Ruby 3.0+ compatibility

## Architecture Overview

This is a Jekyll static site using the minimal theme with custom styling:

### Key Directories
- `_posts/`: Blog posts in Markdown format with YAML front matter
- `_layouts/`: HTML templates (default.html for main layout, resume.html for resume page)
- `_includes/`: Reusable HTML components (nav.html for navigation, social.html for social links)
- `_data/`: YAML data files (resume.yaml contains structured resume data)
- `_site/`: Generated static site (do not edit directly)
- `assets/`: Static assets like images

### Layout System
- Uses Semantic UI CSS framework via CDN
- Main layout (`_layouts/default.html`) creates a grid-based responsive design
- Navigation is auto-generated from site pages using Jekyll's `site.html_pages`
- Social links are configured in `_config.yml` and rendered via `_includes/social.html`

### Content Structure
- Blog posts follow Jekyll naming convention: `YYYY-MM-DD-title.md`
- Resume data is structured in `_data/resume.yaml` and rendered via templates
- All pages use YAML front matter for metadata (title, layout, author)

### Styling
- External Semantic UI CSS loaded from CDN
- Custom CSS in `assets/css/style.css`
- Theme uses jekyll-theme-minimal as base

## Content Guidelines

- Blog posts should be placed in `_posts/` with proper date prefix
- Use YAML front matter with at minimum: layout, title, and author
- Images should be placed in `assets/` directory, organized by post date if post-specific