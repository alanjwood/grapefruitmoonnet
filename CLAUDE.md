# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo static site generator project for grapefruitmoonnet, a personal blog focusing on data, tech (particularly SQL Server and Power BI), music, and sports. The site was migrated from WordPress to Hugo for simplicity and better control.

## Prerequisites

Hugo must be installed to build and develop the site:
- Install Hugo: https://gohugo.io/installation/
- The site uses Hugo's extended version for SCSS/SASS support (recommended)

## Common Commands

### Build and Development

```bash
# Serve the site locally with live reload
hugo server -D

# Serve without drafts
hugo server

# Build the site (output to public/)
hugo

# Build the site with drafts included
hugo -D

# Clean generated files
rm -rf public/ resources/
```

### Content Management

```bash
# Create a new blog post (creates file in content/posts/)
hugo new posts/YYYY-MM-DD-post-title.md

# The archetype (archetypes/default.md) will set up frontmatter with title, date, and draft status
```

## Project Structure

### Content Organization

- **content/posts/** - All blog posts in Markdown format
  - Posts use date-prefixed filenames: `YYYY-MM-DD-title.md`
  - Frontmatter includes: title, author, date, url, categories, tags, and draft status

- **content/about/** - About page

### Custom Layouts (Override Theme Defaults)

- **layouts/index.html** - Homepage showing latest post + recent posts sidebar
- **layouts/_default/single.html** - Individual post pages with recent posts sidebar
- **layouts/_default/list.html** - List pages (categories, tags) with recent posts sidebar

### Custom Styling

- **static/css/custom.css** - Site-specific CSS customizations (automatically loaded by theme)

### Theming and Layout

- **config.toml** - Main site configuration
  - Active theme: `mainroad` (configured in line 4)
  - Note: The mainroad theme directory appears empty and may need to be installed via git submodule or download
  - Google Analytics ID and site metadata are configured here

- **layouts/shortcodes/** - Custom Hugo shortcodes
  - `powerbi.html` - Embeds Power BI reports via iframe
  - Usage in posts: `{{< powerbi "https://app.powerbi.com/view?r=..." >}}`

- **static/** - Static assets (images, CSS, etc.)
  - Images are stored in `static/img/`

- **public/** - Generated static site output (not committed to git, rebuilt on each hugo build)

### Theme Configuration

The site uses the **Risotto** theme (themes/risotto/) - a minimalist, terminal-inspired theme with:
- Plain semantic HTML with no JavaScript
- Plain CSS with no frameworks or preprocessors
- 16 built-in color palettes (currently using `gruvbox-dark`)
- Native responsive design using CSS Grid

Available alternative themes: Coder, Jane (in themes/ directory)

## Post Frontmatter Format

Standard frontmatter structure for blog posts:

```yaml
---
title: "Post Title"
author: alan
type: post
date: YYYY-MM-DDTHH:MM:SSZ
url: /post-slug/
categories:
  - SQL Server
  - Tech
tags:
  - specific-topic
draft: false
---
```

## Site Metadata

- Base URL: https://grapefruitmoon.net/
- Author: Alan Wood
- Main sections: posts
- Pagination: 10 posts per page
- Theme: Risotto (minimalist, terminal-inspired)
- Color palette: gruvbox-dark (can be changed in config.toml)

### Risotto Theme Customization

**Homepage Behavior**: Shows the most recent blog post with full summary, plus 5 recent posts in the sidebar

**Sidebar**: All pages show a "Recent Posts" section with the 5 most recent blog posts

**Color Palette**: Change by updating `params.theme.palette` in config.toml. Available palettes:
- Dark: apprentice, base16-dark, dracula, gruvbox-dark, material, papercolor-dark, solarized-dark, tender, tokyo-night-dark, windows-95
- Light: base16-light, gruvbox-light, papercolor-light, solarized-light, tokyo-night-light, windows-95-light

**Custom Styling**: Edit `static/css/custom.css` to customize appearance

## Important Notes

- Posts with `draft: true` in frontmatter are only visible when running `hugo server -D` or building with `hugo -D`
- The site uses canonical URLs (canonifyurls = true in config.toml)
- Custom shortcodes should be added to `layouts/shortcodes/`
- Syntax highlighting is enabled via pygmentsCodeFences
