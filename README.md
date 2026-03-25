# RailTech B2B — Documentation Hub

Documentation site for the **RailTech B2B Ecosystem** platform — a B2B industrial supply platform for railway equipment, spare parts, and industrial goods in Uzbekistan.

Built with [Hugo](https://gohugo.io/) + [Hextra](https://imfing.github.io/hextra/) theme. Multilingual: Russian (primary) + English.

**Production:** [docs.railtech.uz](https://docs.railtech.uz/)

## Quick Start

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) >= 0.146.0
- [Go](https://go.dev/dl/) >= 1.21
- [Node.js](https://nodejs.org/) >= 20

### Installation

```bash
# Clone with submodules
git clone --recurse-submodules git@github.com:nzulkaynarov/RailTech_SaaS_Docs.git
cd RailTech_SaaS_Docs

# Install Node dependencies (PostCSS/Tailwind)
npm install
```

### Development

```bash
# Run dev server with drafts
npm run dev
# http://localhost:1313

# Run dev server (published content only)
npm run serve
```

### Production Build

```bash
npm run build
# Output: ./public/
```

## Project Structure

```text
.
├── content/                    # Markdown content (RU + EN)
│   ├── _index.md               # Homepage
│   └── docs/
│       ├── api/                # REST API & WebSocket docs
│       ├── architecture/       # System design & security audit
│       ├── guides/
│       │   ├── clients/        # Buyer guides (8 pages)
│       │   ├── suppliers/      # Supplier guides (3 pages)
│       │   └── managers/       # Internal CRM guides (5 pages)
│       ├── knowledge-base/     # Glossary, RBAC, domains, finance
│       ├── changelog/
│       └── archive/
├── themes/hextra/              # Hextra theme (git submodule)
├── layouts/shortcodes/         # Custom shortcode overrides
├── data/                       # API reference data (YAML/TOML)
├── i18n/                       # Translation strings (ru.toml, en.toml)
├── static/images/              # Static assets
├── hugo.toml                   # Hugo configuration
├── go.mod                      # Hugo module dependencies
├── package.json                # Node.js scripts & PostCSS deps
├── frontmatter.json            # Front Matter CMS config
└── .github/workflows/deploy.yml # GitHub Pages CI/CD
```

## Content Guide

### Adding a new page

Create a markdown file in the appropriate section:

```bash
# Example: new client guide
touch content/docs/guides/clients/new-feature.md
```

Required frontmatter:

```yaml
---
title: "Page Title"
linkTitle: "Short Title"
weight: 50          # Sort order within section
description: "One-line description for SEO and cards"
categories: ["Руководства"]
tags: ["tag1", "tag2"]
---
```

### Available shortcodes

- `{{</* tabs */>}}` + `{{</* tab name="Name" */>}}` — tabbed content
- `{{</* callout type="info|warning|error" */>}}` — callout blocks
- `{{</* card link="..." title="..." icon="..." */>}}` — navigation cards
- `{{</* details title="..." */>}}` — expandable sections
- `{{</* steps */>}}` — numbered steps

### Screenshot placeholders

All `<!-- TODO: заменить на скриншот ... -->` comments mark places where real screenshots should replace the SVG placeholder. Search for them:

```bash
grep -r "TODO: заменить на скриншот" content/
```

## Deployment

Automated via GitHub Actions on push to `main` branch. Deploys to GitHub Pages.

To enable: **Settings > Pages > Source: GitHub Actions**.

## Tech Stack

| Component              | Version    |
| ---------------------- | ---------- |
| Hugo Extended          | >= 0.146.0 |
| Hextra theme           | 0.12.1     |
| Go                     | >= 1.21    |
| Node.js                | >= 20      |
| PostCSS + Tailwind CSS | v4         |

## License

Copyright ООО "RailTech". All rights reserved.
