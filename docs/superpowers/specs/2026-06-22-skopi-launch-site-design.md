# skopi.github.io Launch Site — Design

**Date:** 2026-06-22
**Status:** Approved (pending implementation)

## Overview

A minimal Jekyll-powered GitHub Pages site for skopi served from
`https://skopi.github.io`. The site has two pages:

1. A **launch landing page** — centered logo plus a "We are launching…" message.
2. A **privacy policy** page — the existing privacy policy converted to Markdown
   and rendered through a shared Jekyll layout.

Selected brand identity: header/title display name is **skopi.ai**. The privacy
contact email remains **privacy@skopi.com**.

## Goals

- Simple, responsive, no-JavaScript launch page using the brand palette.
- Privacy policy reachable at `/privacy/`, sharing the site's header/footer/styles.
- A small set of values editable via `_config.yml` without touching HTML:
  brand name, tagline, and an optional notify link.

## Non-Goals (YAGNI)

- No analytics, contact form, or newsletter backend.
- No Terms of Service page (the policy's ToS link is dropped/neutralized).
- No custom domain / CNAME — the site stays on `skopi.github.io` (a CNAME was
  recently deleted from the repo, so we do not reintroduce one).

## File Layout

```
skopi.github.io/
├── _config.yml            # configurable: brand name, tagline, notify link, description
├── _layouts/
│   ├── default.html       # <head>, header (logo + brand), footer, shared CSS link
│   └── page.html          # extends default, wraps content in a container (for the policy)
├── index.html             # launch landing page
├── privacy.md             # privacy policy in Markdown, layout: page
├── assets/
│   ├── skopi-mark.png     # logo (already downloaded, 300x300 PNG)
│   └── css/style.css      # shared stylesheet (teal/cyan palette)
├── Gemfile                # github-pages gem (for local preview)
└── .gitignore             # _site/, vendor/, .jekyll-cache/, .bundle/
```

## Components

### `_config.yml`

Configurable values (the only fields intended for routine editing):

```yaml
title: skopi.ai            # brand name shown in header and <title>
tagline: "<launch subtext>" # subtext under the "We are launching…" headline
notify_link: ""            # mailto: address or URL; the button is hidden when empty
description: "<SEO meta description>"
```

Jekyll/Pages plumbing (`theme`/plugins as needed) also lives here but is not part
of the routine-edit surface.

### `_layouts/default.html`

- `<head>` with `<title>`, viewport meta, `{{ site.description }}` meta, link to
  `assets/css/style.css`.
- Header: `skopi-mark.png` logo plus `{{ site.title }}` (skopi.ai).
- Footer: copyright and a link to the Privacy Policy.
- `{{ content }}` slot.

### `_layouts/page.html`

- Uses `layout: default`.
- Wraps `{{ content }}` in a `.container` (max-width ~900px) for readable
  long-form content like the policy.

### `index.html`

- Single-screen, vertically centered layout.
- Elements, top to bottom: logo (~140px), "We are launching…" headline,
  `{{ site.tagline }}` subtext, optional "Notify me" button (rendered only when
  `site.notify_link` is non-empty), and a footer link to the Privacy Policy.
- The "We are launching…" headline text stays in the markup; the tagline below it
  is config-driven.
- No JavaScript; system font stack; fully responsive.

### `privacy.md`

- `layout: page`, `title: Privacy Policy`, permalink `/privacy/`.
- Content is the existing policy converted to clean Markdown, preserving all 13
  sections and the "Last Updated: June 21, 2026" line.
- The colored callout boxes (important / highlight / contact-info) are reproduced
  via CSS classes in the shared stylesheet (using inline HTML spans/divs within
  the Markdown where needed).
- Contact email stays `privacy@skopi.com`.

### `assets/css/style.css`

- Palette derived from the logo: dark teal background `#0e2a2a`, cyan accent
  (~`#5eead4`), light text. The policy page may use a lighter content background
  for readability while keeping the teal header/accents.
- Defines: base typography, header, footer, landing-page layout, content
  container, and the callout box classes (`.important`, `.highlight`,
  `.contact-info`).
- Responsive breakpoints comparable to the original policy (~768px).

## Data Flow

Static site, no runtime data. Jekyll renders templates with `_config.yml` values
at build time. GitHub Pages builds from the repository root on `main` and serves
the generated `_site/`.

## Error Handling

- If `notify_link` is empty, the notify button is omitted (no broken/empty link).
- Logo is a local asset, so the landing page does not depend on an external host.

## Testing / Verification

- Local preview via `bundle exec jekyll serve` (Gemfile pins the `github-pages`
  gem) to confirm both pages render and the logo loads.
- Manual checks: landing page centered and responsive; `/privacy/` renders all
  sections with callouts styled; brand name reads "skopi.ai"; privacy email
  intact; changing `tagline`/`notify_link` in `_config.yml` reflects on the page.
