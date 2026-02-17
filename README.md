# Grapefruitmoon.net

Hugo source for [Grapefruitmoon.net](https://grapefruitmoon.net/) - a personal blog about data, tech, music, sports, and other related topics.

## About

This is a static site built with [Hugo](https://gohugo.io/) using the minimalist [Risotto](https://github.com/joeroe/risotto) theme. The site features blog posts primarily focused on:

- SQL Server & SSIS
- Power BI & data analysis
- Tech & productivity
- Music & sports

## Features

- 🎨 **Minimalist Design** - Terminal-inspired Risotto theme with gruvbox-dark palette
- 📱 **Responsive** - Mobile-friendly CSS Grid layout
- 🚀 **Fast** - Static HTML with no JavaScript
- 📝 **Latest Post Homepage** - Shows most recent article on the homepage
- 📚 **Recent Posts Sidebar** - 5 most recent posts on every page
- 🎯 **Custom Layouts** - Enhanced homepage and post pages
- 🖼️ **Optimized Images** - Responsive image sizing

## Quick Start

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) v0.155+

### Local Development

```bash
# Clone the repository
git clone https://github.com/alanjwood/grapefruitmoonnet.git
cd grapefruitmoonnet

# Run development server with drafts
hugo server -D

# Run production preview (without drafts)
hugo server

# Visit http://localhost:1313/
```

### Building

```bash
# Build the site (output to public/)
hugo

# Build with drafts included
hugo -D
```

## Project Structure

```
.
├── content/
│   ├── about/          # About page
│   └── posts/          # Blog posts (YYYY-MM-DD-title.md)
├── layouts/
│   ├── _default/
│   │   ├── list.html   # List pages with recent posts sidebar
│   │   └── single.html # Post pages with recent posts sidebar
│   └── index.html      # Custom homepage layout
├── static/
│   ├── css/
│   │   └── custom.css  # Site-specific styling
│   └── img/            # Images and assets
├── themes/
│   ├── risotto/        # Active theme
│   ├── coder/          # Alternative theme
│   └── jane/           # Alternative theme
├── config.toml         # Site configuration
└── CLAUDE.md           # Development documentation
```

## Configuration

The site is configured in `config.toml`:

- **Theme**: Risotto with gruvbox-dark color palette
- **Pagination**: 10 posts per page
- **Syntax Highlighting**: Monokai style
- **Social Links**: Bluesky profile

To change the color scheme, edit the `palette` setting:
```toml
[params.theme]
palette = "gruvbox-dark"  # Try: dracula, solarized-dark, tokyo-night-dark, etc.
```

## Creating New Posts

```bash
# Create a new post
hugo new posts/YYYY-MM-DD-post-title.md

# Edit the post in content/posts/
# Set draft: false when ready to publish
```

## Deployment

The site includes GitHub Actions for automatic deployment. See [DEPLOYMENT.md](DEPLOYMENT.md) for detailed setup instructions.

### Supported Methods

- **FTP/FTPS** - For shared hosting (cPanel, Plesk)
- **SSH/rsync** - For VPS/cloud hosting
- **Manual** - Build locally and upload `public/` folder

## Customization

### Styling

Edit `static/css/custom.css` to customize:
- Text sizes and fonts
- Image sizing and spacing
- Colors and layout

### Layouts

Override theme layouts by creating files in `layouts/`:
- `layouts/index.html` - Homepage
- `layouts/_default/single.html` - Individual posts
- `layouts/_default/list.html` - List pages (categories, tags)

### Shortcodes

Custom shortcodes in `layouts/shortcodes/`:
- `powerbi.html` - Embed Power BI reports

Usage in posts:
```markdown
{{< powerbi "https://app.powerbi.com/view?r=..." >}}
```

## Development

See [CLAUDE.md](CLAUDE.md) for:
- Build commands
- Content organization
- Theme configuration
- Development tips

## License

Content © Alan Wood. All rights reserved.

Theme: [Risotto](https://github.com/joeroe/risotto) by Joe Roe (MIT License)

## Contact

- **Website**: [grapefruitmoon.net](https://grapefruitmoon.net/)
- **Bluesky**: [@alanwood.me](https://bsky.app/profile/alanwood.me)
