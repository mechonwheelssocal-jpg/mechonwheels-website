# Mech on Wheels — Website Codebase Guide

## Project Overview

Static marketing/landing page for **Mech on Wheels**, a mobile mechanic business serving Southern California (Orange County & Los Angeles County). Deployed on GitHub Pages at `mechonwheels.info`.

**Stack:** Pure vanilla HTML, CSS, and JavaScript — no build tools, no frameworks, no package manager.

---

## Repository Structure

```
mechonwheels-website/
├── index.html        # Entire website (HTML + inline CSS + inline JS)
├── logo.jpg          # Original logo
├── logo_purple.jpg   # Purple-branded logo (used in About section)
└── CNAME             # GitHub Pages custom domain → mechonwheels.info
```

All styles live in a `<style>` block inside `<head>`. All scripts live in a `<script>` block at the bottom of `<body>`. There is no external CSS file, no JS modules, and no build step.

---

## Page Sections (in order)

| Section | Anchor | Notes |
|---|---|---|
| Navigation | — | Fixed top bar; collapses to hamburger on mobile |
| Hero | — | `<header class="hero">` with two CTA buttons |
| About | `#about` | Two-column grid with `logo_purple.jpg` |
| Services | `#services` | Auto-fit card grid; 6 service cards |
| How It Works | `#how-it-works` | 3-step card grid + service area banner |
| Contact | `#contact` | Left: contact info; Right: service request form |
| Footer | — | Brand blurb + service links |

---

## Design System (CSS Custom Properties)

Defined on `:root` in `index.html:24–42`:

| Variable | Value | Usage |
|---|---|---|
| `--bg-dark` | `#0a0a0f` | Page background |
| `--bg-card` | `#13131a` | Card backgrounds |
| `--bg-alt` | `#1a1a24` | Alternate section backgrounds |
| `--text-main` | `#fefce8` | Body text (warm off-white) |
| `--text-muted` | `#cbd5e1` | Secondary/muted text |
| `--text-heading` | `#fde047` | All `h1–h4` elements |
| `--accent` | `#9333ea` | Primary purple accent |
| `--accent-hover` | `#a855f7` | Purple on hover |
| `--accent-second` | `#fbbf24` | Secondary yellow accent |
| `--border` | `rgba(255,255,255,0.07)` | Subtle borders |
| `--border-light` | `rgba(255,255,255,0.15)` | Stronger borders |
| `--radius` | `8px` | Card/input border radius |
| `--transition` | `all 0.2s ease-in-out` | Standard transition |

**Font:** Inter (Google Fonts CDN), weights 300/400/500/600/700/800.

---

## Contact Form

Located at `index.html:699–758`. Submits via **Formspree AJAX** to:
```
https://formspree.io/f/xkoqzval
```

Form fields sent to Formspree:
- `name` — customer full name
- `phone` — phone number
- `location` — city or zip code
- `vehicle` — assembled string (hidden input `#vehicleData`)
- `concern` — free-text description of the issue
- `_vehicle_type` — radio value (`dropdowns` or `vin`); prefixed with `_` so Formspree ignores it

### Vehicle Selection Logic (`index.html:833–896`)

Two modes toggled by radio buttons:

**Dropdown mode (default):**
1. Year select populated with 1990 to `currentYear + 1` (JS at runtime)
2. Make select populated from a hardcoded `popularMakes` array (35 brands)
3. Model select fetched from the **NHTSA API**:
   ```
   https://vpic.nhtsa.dot.gov/api/vehicles/GetModelsForMakeYear/make/{make}/modelyear/{year}/vehicleType/passenger%20car?format=json
   ```
4. Before submission, `#vehicleData` is set to `"YYYY Make Model"`

**VIN mode:**
- Shows a single text input for 17-digit VIN
- Before submission, `#vehicleData` is set to `"VIN: XXXXXXXXXXXXXXXXX"`

### Form Submission Flow (`index.html:898–947`)

1. `handleSubmit` intercepts the submit event
2. Assembles `vehicleData` hidden field
3. Disables submit button, sets text to "Sending..."
4. Posts via `fetch()` with `Accept: application/json`
5. On success: shows green status message, resets form
6. On error: shows red status message with error details
7. `finally`: re-enables submit button

---

## Responsive Breakpoints

| Breakpoint | Behavior |
|---|---|
| `> 900px` | Two-column about/contact grids |
| `≤ 900px` | Stacks about and contact grids to single column |
| `≤ 768px` | Hides desktop nav links, shows hamburger menu |

### Mobile Menu (`index.html:797–826`)

The hamburger toggle (`button.menu-toggle`) uses inline style manipulation to show/hide `.nav-links`. On resize above 768px, styles are reset to restore the desktop layout.

---

## Deployment

- **Hosting:** GitHub Pages
- **Domain:** `mechonwheels.info` (set via `CNAME` file)
- **Branch:** `main` is the live branch
- **Deploy:** Pushing to `main` triggers automatic GitHub Pages deployment — no CI pipeline, no build step

To update the site, edit `index.html` and push to `main`. Changes are live within ~1–2 minutes.

---

## Development Conventions

- **All changes go in `index.html`** — there is no separate CSS or JS file to maintain.
- **Inline styles** are used sparingly in HTML for one-off layout tweaks (e.g., `grid-template-columns` on a single form row). Reusable styles belong in the `<style>` block.
- **No comments** in JS unless the logic is non-obvious. CSS comments are used to label major sections.
- **No frameworks.** Do not introduce React, Vue, jQuery, or any npm package. This is intentional — the site must remain a single deployable HTML file.
- **No build step.** Do not add webpack, Vite, or any bundler.
- **External dependencies** (three, all CDN):
  - Google Fonts Inter — loaded in `<head>` via `<link>`
  - NHTSA VPIC API — called at runtime via `fetch()` in `fetchModels()`
  - Google Analytics 4 — inline loader in `<head>`; stays dormant until the `G-XXXXXXXXXX` placeholder Measurement ID is replaced with a real one
- When adding new sections, follow the existing pattern: `.section` class for padding, `.section-alt` for the alternating dark background, `.uppercase-label` for eyebrow text above headings.

---

## Business Contact Info (for content edits)

- **Phone:** (714) 724-4696
- **Email:** mechonwheels.socal@gmail.com
- **Service area:** Orange County & Los Angeles County, Southern California
