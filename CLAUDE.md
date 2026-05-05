# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Static marketing site for **Magnum Torque** (Curitiba-PR), a Brazilian shop that rebuilds automatic-transmission torque converters. The site is plain HTML + CSS + vanilla JS, deployed by uploading files to Hostinger (Apache). Locale is `pt-BR` throughout.

There is **no application build step**, **no package.json**, and **no backend**. The only tooling is one Node script that regenerates the blog search index.

## Commands

| Task | Command |
|------|---------|
| Regenerate blog search index | `node scripts/build-search.mjs` |
| Same, with AI-generated PT synonyms | `$env:ANTHROPIC_API_KEY="sk-ant-..."; node scripts/build-search.mjs` (PowerShell) |
| Local preview (any static server) | `npx http-server . -p 8080` |
| Run the same checks CI runs | `npx -y html-validate@9.x '**/*.html' --ignore .git --ignore node_modules` and `npx -y linkinator@6.x . --recurse --skip "^https?://(localhost\|127\\."` |

CI (`.github/workflows/`):
- `html-validate.yml` — html-validate + linkinator on push/PR to `main`
- `lighthouse.yml` — Lighthouse CI with thresholds (perf 0.85, a11y 0.95, best-practices 0.90, SEO 0.95). Assertions use `|| true`, so they're informational, not blocking.

## Architecture

### Page layout

- `/index.html` — **single-page** home. All sections (`#inicio`, `#sobre`, `#servicos`, `#pecas`, `#parceiros`, `#depoimentos`, `#faq`, `#contato`) are anchors on this one file.
- `/blog/index.html` — manually-curated list of post cards (each card has `data-slug`).
- `/blog/<slug>/index.html` — one HTML file per post. Currently one post: `sinais-conversor-torque-precisa-retifica/`.
- `/404.html`, `sitemap.xml`, `robots.txt`, `manifest.webmanifest`, `humans.txt`, `.well-known/security.txt` — standard SEO/PWA boilerplate.
- `/.htaccess` — Apache config (Hostinger). Forces HTTPS + `www`, redirects legacy paths like `/sobre` → `/#sobre`, denies direct access to `.md`/`.json`/`.yml`/`.lock` files **except** whitelisted ones (`search-index.json`, `robots.txt`, `sitemap.xml`, `humans.txt`). Sets long-cache headers on static assets and 0-second cache on HTML. Anything you change here only takes effect on the production server.

### JavaScript

Two scripts, both vanilla, no bundler:

- **`js/main.js`** — runs on every page. Each feature is a self-invoking IIFE that no-ops if its DOM hook is missing, so the same script works on home and blog pages:
  - Header: mobile nav toggle, scroll shadow, scrollspy. **Scrollspy is disabled when `<body class="blog-page">`** because post bodies may have section IDs (`#faq`, etc.) that collide with the menu — without this guard, scrolling inside a post would highlight wrong nav items.
  - Hash-on-load fix that re-offsets when arriving via `/#secao` (compensates for the fixed 80px header).
  - Business-hours indicator (`[data-business-hours]`): computes open/closed in `America/Sao_Paulo`, Mon–Fri 08:00–18:00, refreshes every minute.
  - **Contact form**: validates client-side, then opens `https://wa.me/554135036828?text=...` (URL-encoded message) in a new tab. There is **no backend**. Has a honeypot field (`#hp-field`) — if filled, the submission is silently dropped. Fires GA4 events `generate_lead` and `form_submit`.
  - Conversion tracking: a single delegated `click` listener on `document` fires GA4 events for `click_whatsapp`, `click_phone`, `click_email`. The `location` parameter comes from the nearest ancestor with an `id`.
  - Testimonials carousel, modal (focus-trap + ESC), back-to-top.

- **`js/blog-search.js`** + **`js/vendor/minisearch.min.js`** — only loaded by `/blog/index.html`:
  - On first focus of the search input, fetches `/blog/search-index.json` (`cache: 'force-cache'`) and builds a MiniSearch index over `title`/`excerpt`/`category`/`body`/`synonyms` with field boosts.
  - Filters the **already-rendered** post cards in the DOM (toggling `hidden`, reordering by relevance) — it does **not** re-render the list. Cards' original innerHTML is captured at load time so highlights can be cleared.
  - Both indexing and queries normalize via `text.normalize('NFD').replace(/[̀-ͯ]/g, '')` so accented searches match unaccented text.
  - Two-pass fuzzy fallback: first `fuzzy: 0.2 + AND`; if zero hits, retries with `fuzzy: 0.4 + AND` (still requires every term to match — avoids irrelevant results from a single fuzzy hit).
  - Debounce 200ms, minimum 2 chars, fires GA4 `search` event with `search_term` + `results`.

### Blog search index pipeline

`scripts/build-search.mjs` walks `blog/<slug>/index.html`, extracts `title` (from `og:title` → `.post-title` → `<title>`), `excerpt`, `category`, `date`, `image`, `readTime` (regex: `(\d+)\s*min de leitura`) and the post body (capped at 6000 chars). HTML comments are stripped before extraction so `<!-- /post-content -->` etc. don't break the closing-div anchors. For each post it merges:
1. Manual synonyms from `blog/<slug>/synonyms.json` (optional)
2. AI-generated synonyms from Claude (only when `ANTHROPIC_API_KEY` is set in env) — uses model `claude-sonnet-4-6` with a Brazilian-Portuguese-flavored prompt asking for 30–60 search-intent phrases including OBD-II codes, EN technical terms, colloquial symptom descriptions

Output `blog/search-index.json` is the only generated file in the repo and **must be committed** when posts change. The `.htaccess` whitelists this file for client `fetch`.

### Adding a new blog post

1. Create `blog/<new-slug>/index.html` with the required meta tags: `og:title`, `og:description`, `og:image`, `article:published_time` (ISO date), `article:section` (category), `<meta name="description">`, `<link rel="canonical">`. Wrap the post content in `<article class="post"><div class="post-content">...</div></article>` so the indexer can extract it.
2. Optionally create `blog/<new-slug>/synonyms.json` — a JSON array of strings (see existing post for the format).
3. Add a `<article class="blog-list-card" data-slug="<new-slug>">` block to `blog/index.html`, plus a `BlogPosting` entry to the JSON-LD `Blog` schema in the same file.
4. Add a `<url>` block to `sitemap.xml`.
5. Run `node scripts/build-search.mjs` (with `ANTHROPIC_API_KEY` if you want AI synonyms — costs ~$0.01/post).
6. Commit the new files **and** the updated `blog/search-index.json`.

`notes/post-ideas.md` is an internal roadmap of planned posts — never deployed (it's a `.md`, denied by `.htaccess` anyway, and `robots.txt` disallows the directory).

## Conventions to preserve

- **No frameworks, no bundler, no transpiler.** Plain HTML, CSS, ES2017+ JS. Don't introduce React/Vue/Vite/Tailwind unless the user explicitly asks.
- **Header markup is duplicated** between `index.html` and `blog/index.html` (and between desktop/mobile nav within each page). When changing nav items, update all four locations.
- **All icons are inline SVG.** Shared icons live in a `<svg style="display:none">` block at the top of each page as `<symbol>` elements, used via `<use href="#icon-name"/>`. The two icons swapped at runtime (hamburger ↔ X) are template literals in `js/main.js`.
- **CSP** is set via `<meta http-equiv="Content-Security-Policy">` in every HTML page and is strict. The home page allows `frame-src https://www.google.com https://maps.google.com` for the embedded map; blog pages omit those (no map). Any new third-party script/connect/frame source needs an explicit allowance there. The contact form does NOT POST anywhere — it builds a `wa.me` URL and calls `window.open`, so no `form-action` allowlist is needed.
- **Schema.org JSON-LD** is hand-written in each page (`AutoRepair` on home, `Blog` + `BlogPosting` + `BreadcrumbList` on blog). Keep it in sync when content changes (review counts, post lists, dates).
- **Phone/WhatsApp number** `+55 41 3503-6828` and email `contato@magnumtorque.com.br` are hard-coded in many places (header, footer, forms, schema, manifest shortcuts, .htaccess wa.me links). Search before changing.
- **GA4 measurement ID** `G-0HJYEEB2XR` is hard-coded in each HTML page's `<head>` gtag snippet.
- **Production host** is `https://www.magnumtorque.com.br/` (note the `www`). All canonical URLs and `og:url` use that exact form; `.htaccess` 301-redirects the apex.
