# CLAUDE.md — Project Context for AI Assistants

## Project Overview

This is the **documentation site** for the RailTech B2B Ecosystem — a B2B industrial supply platform for railway equipment in Uzbekistan. Built with Hugo (static site generator) + Hextra theme. Multilingual: Russian (primary) + English (partial).

**Production URL:** https://docs.railtech.uz/
**Repository:** github.com/nzulkaynarov/RailTech_SaaS_Docs

## Tech Stack

- **Hugo Extended** >= 0.146.0 (static site generator)
- **Hextra** v0.12.1 (documentation theme, git submodule at `themes/hextra/`)
- **Go** >= 1.21 (Hugo modules)
- **Node.js** >= 20 (PostCSS + Tailwind CSS v4)
- **Front Matter CMS** (VS Code content management)

## Key Commands

```bash
npm run dev          # Dev server with drafts: http://localhost:1313
npm run serve        # Dev server without drafts
npm run build        # Production build → ./public/
hugo --minify        # Direct Hugo build with minification
```

## Project Structure

- `content/` — All markdown content (RU default, `.en.md` for English)
- `content/docs/api/` — REST API & WebSocket documentation
- `content/docs/architecture/` — System design, security audit, UX brief
- `content/docs/guides/clients/` — Buyer guides (registration → team management)
- `content/docs/guides/suppliers/` — Supplier guides (onboarding → offers)
- `content/docs/guides/managers/` — Internal CRM guides (deals → sourcing)
- `content/docs/knowledge-base/` — Glossary (99 terms), RBAC, domains, finance
- `themes/hextra/` — Theme (git submodule, DO NOT edit directly)
- `layouts/shortcodes/` — Custom shortcode overrides (only `callout.html`)
- `data/` — API reference data (`api.yaml`, `params.toml`)
- `i18n/` — Translation strings (`ru.toml`, `en.toml`)
- `static/images/` — Static assets (screenshots, SVG placeholders)
- `.github/workflows/deploy.yml` — GitHub Pages CI/CD

## Content Conventions

### Frontmatter (required for every page)

```yaml
---
title: "Full Page Title"
linkTitle: "Short Nav Title"
weight: 10              # Sort order (10, 20, 30...)
description: "One-line SEO description"
categories: ["Руководства"]  # One of: Руководства, API, Архитектура, База знаний, Архив, Changelog
tags: ["tag1", "tag2"]
---
```

### Language files

- Russian (default): `filename.md`
- English: `filename.en.md`
- Section indexes: `_index.md` / `_index.en.md`

### Shortcodes (Hextra theme)

```markdown
{{</* tabs */>}}
  {{</* tab name="TabName" */>}} content {{</* /tab */>}}
{{</* /tabs */>}}

{{</* callout type="info" */>}} Note text {{</* /callout */>}}
{{</* callout type="warning" */>}} Warning text {{</* /callout */>}}

{{</* card link="/docs/section/" title="Title" icon="icon-name" */>}}
{{</* details title="Expandable" */>}} hidden content {{</* /details */>}}
```

**IMPORTANT:** Do NOT use deprecated `items` parameter on `tabs`. Use `name` on each `tab` instead.

### Screenshot placeholders

Places where real screenshots are needed are marked with:
```markdown
<!-- TODO: заменить на скриншот ... -->
![Alt text](/images/screenshots/placeholder.svg)
```

To find all: `grep -r "TODO: заменить на скриншот" content/`

When replacing: keep the `![alt](path)` format, update path to actual image, remove the HTML comment.

## Architecture Notes

### Platform being documented

The RailTech B2B platform uses:
- **Backend:** Django + DRF, Celery + Redis, PostgreSQL (pg_trgm for Smart Match)
- **Frontend:** React (SPA)
- **Real-time:** Django Channels (WebSocket) for deal chat
- **Auth:** JWT (access 5min / refresh 1day)
- **Financial model:** FCA → Landed Cost → Margin → VAT 12% (all in UZS)

### User roles documented

| Role | Access |
| ---- | ------ |
| GUEST | Public catalog (no prices) |
| CLIENT | Full catalog + deals + chat |
| SALES_MANAGER | Own deals + Sourcing Hub |
| ACCOUNT_MANAGER | All deals + analytics |
| ADMIN | Full access + KYC verification |

### Business domains

- **Railway Equipment** — strict hierarchy, requires EquipmentGroup
- **General Goods** — flexible JSONB attributes
- **Services** — simplified pricing (no logistics/customs)

## Rules for AI

1. **Language:** Content pages are in Russian. Technical docs (API) may be in English. Always match the language of the file you're editing.
2. **No emojis** in content files unless the user explicitly asks.
3. **Verify build** after content changes: `hugo --minify` must produce 0 errors, 0 warnings.
4. **Theme is read-only.** Never edit files in `themes/hextra/`. Override via `layouts/` if needed.
5. **Hugo shortcode syntax** — use `{{</* shortcode */>}}` (raw) not `{{%/* shortcode */%}}` (markdown).
6. **Weights** — use multiples of 10 (10, 20, 30...) for sorting, leave gaps for future pages.
7. **Internal links** — use relative paths: `[Link text](../sibling-page/)` with trailing slash.
8. **Keep frontmatter complete** — always include title, linkTitle, weight, description, categories, tags.
9. **Images** go to `static/images/` — reference as `/images/filename.ext` in markdown.
10. **Don't commit** `public/`, `_gen/`, `node_modules/`, `.hugo_build.lock` — they're in `.gitignore`.
