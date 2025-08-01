# Fulton Byrne's Personal Website

A Jekyll-based personal website and blog hosted on GitHub Pages.

## Development

### Local Setup
```bash
bundle install
bundle exec jekyll serve --detach --watch &
```

The site will be available at http://localhost:4000

### Building for Production
```bash
bundle exec jekyll build
```

## Analytics

Site visits are tracked using **Umami Cloud** - a privacy-focused analytics platform.

- **Analytics Dashboard**: https://cloud.umami.is

The tracking script is lightweight and respects user privacy by not using cookies or collecting personal data.

## Site Structure

- `_posts/`: Blog posts in Markdown format
- `_layouts/`: HTML templates for pages
- `_includes/`: Reusable components
- `_data/`: Structured data (resume info)
- `assets/`: Static assets (CSS, images)

## Key Features

- Responsive design with mobile-first approach
- SEO optimized with jekyll-seo-tag
- Social media integration
- Clean, minimal aesthetic
- Fast loading with optimized images