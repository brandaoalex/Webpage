# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

Modern Resume Theme is a Jekyll-based static site generator for creating professional resumes. It's designed as both a Ruby gem and a GitHub Pages theme, allowing users to host their resumes for free on GitHub Pages.

## Development Commands

### Local Development
```bash
# Install dependencies
bundle install

# Serve the site locally with live reload
bundle exec jekyll serve

# Serve with specific config (useful for testing)
bundle exec jekyll build --config _test/_config-version-2.yml
```

### Docker Development
```bash
# Run with Docker Compose (serves on localhost:4000)
docker-compose up
```

### Testing
```bash
# Build test sites for both configuration versions
bundle exec jekyll build --config _test/_config-version-1.yml --destination ./_test-site-1
bundle exec jekyll build --config _test/_config-version-2.yml --destination ./_test-site-2

# Run HTML validation tests
bundle exec htmlproofer ./_test-site-1 --disable-external
bundle exec htmlproofer ./_test-site-2 --disable-external
```

### Gem Development
```bash
# Build the gem
gem build modern-resume-theme.gemspec

# Install locally for testing
gem install modern-resume-theme-*.gem
```

## Architecture Overview

### Configuration System
The theme supports two configuration versions:
- **Version 1**: Legacy format for backward compatibility
- **Version 2**: Modern, flexible content structure (recommended)

Version 2 uses a dynamic content array in `_config.yml` that allows users to add unlimited sections in any order with two layout types: `list` and `text`.

### Template Structure
- `_layouts/default.html` - Main template that conditionally renders v1 or v2 content
- `_includes/` - Modular components (header, footer, sections)
- `_sass/` - SCSS stylesheets with responsive design and dark mode support
- `_test/` - Test configurations for both versions

### Content Rendering
The theme uses Jekyll's Liquid templating to dynamically generate sections:
```liquid
{% for section in site.content %}
  <div class="container {{ section.layout }}-container">
    <h3 id="{{ section.title | slugify }}">{{ section.title }}</h3>
    {% include {{ section.layout | prepend: "section-" | append: ".html" }} content=section.content %}
  </div>
{% endfor %}
```

### Layout System
Flexible positioning system with multiple options:
- `left`, `right` - Side-by-side layouts (responsive)
- `top`, `top-right`, `top-middle` - Stacked layouts
- Responsive breakpoints automatically stack layouts on mobile

### Styling Architecture
- Base SCSS in `_sass/` with modular imports
- Dark mode support with class-based theming
- Print-specific styles for resume printing
- Responsive design with mobile-first approach

## Key Configuration Points

### Theme Modes
- `darkmode: true` - Always dark theme
- `darkmode: false` - Respects user system preference
- `darkmode: never` - Always light theme

### Content Structure (Version 2)
Each content section requires:
- `title` - Section heading
- `layout` - Either `list` or `text`
- `content` - Array of items (for list) or markdown text (for text)

### Social Links
Supports built-in social platforms plus unlimited `additional_links` with Font Awesome icons.

## File Organization

### Core Theme Files
- `modern-resume-theme.gemspec` - Gem specification
- `lib/modern-resume-theme/version.rb` - Version management
- `_config.yml` - Main configuration template
- `Gemfile` - Ruby dependencies

### Development Files
- `_test/` - Test configurations and assets
- `.github/workflows/ci-workflow.yml` - Automated testing and deployment
- `docker-compose.yml` - Docker development environment
- `Procfile` - Heroku deployment configuration

### User-Customizable Files
- `_config.yml` - All content and configuration
- `assets/main.scss` - Custom styling overrides
- `images/` - Profile photos and other assets

## Testing Strategy

The project maintains backward compatibility by testing both configuration versions. The CI workflow builds separate sites for v1 and v2 configurations and validates HTML output with htmlproofer.

Changes to core functionality should be tested against both `_test/_config-version-1.yml` and `_test/_config-version-2.yml` to ensure compatibility.
