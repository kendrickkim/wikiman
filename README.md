[한국어](README-kr.md)

# Wikiman

Hub documentation for the personal wiki **Wikiman** and its related repositories.

Wikiman is meant for a single author. It covers posts, categories, attachments,
quick posts, backup, link previews, and PlantUML. Only the first registered
account can be a `writer`.

This repository is **not** a monorepo of source code. It is a **hub** that points
at the projects that make up Wikiman.

## Highlights

Wikiman combines a document wiki and a personal blog in one self-hosted product.
Unlike wiki-only tools (Notion, Wiki.js) or blog-only tools (Ghost, WordPress),
you can organize knowledge and publish chronologically in the **same data and UI**.

- **Wiki + blog hybrid** — category tree, keywords, and search for knowledge;
  optional blog mode turns the home page into a recent published-post feed. A
  designated home page post can sit above the feed.
- **Per-post editors** — Text, CKEditor, Summernote, TUI, Editor.js, Markdown,
  and HTML. Switching editors keeps body content when a conversion is possible.
- **Light self-hosting** — SQLite plus an uploads directory. Back up `data/` to
  move hosts. The same API runs on **Node (Express)** or **Apache + PHP**.
- **Clear draft vs published** — drafts, publish, public/private posts and
  categories, and a writer-only trash (restore / hard delete). Draft views can
  publish after confirmation.
- **Serious Markdown** — PlantUML, code highlight, line numbers, copy buttons.
  Server-side Open Graph so crawlers get title, summary, and image.
- **Daily writing UX** — mobile quick posts, PWA, top menu, site language
  (한국어 / English), font size, dark mode, category panel defaults, DokuWiki
  import.

In one line: **a personal wiki structure with blog-style publishing, without
SaaS or a heavy CMS**.

## Main features

- Category tree (public/private), keywords, search, trash
- Home page post / blog feed (page size, home post first)
- Multiple editors with body hand-off on switch
- Draft · publish, public · private, publish from draft view
- Markdown highlight · line numbers · copy, PlantUML
- Quick posts and promotion to full posts
- Attachments and file access control
- Link-preview cache (SSRF protection), backup/restore (`.wkmbak`)
- Site admin (general, categories, home, blog, quick posts, attachments, data,
  top menu)
- Site language (한국어 / English), PWA, server-side Open Graph / Twitter cards
- Auth: JWT; only the first signup is a writer
- Android·iOS admin app (WebView, secure connection store, share → quick post)

## Projects

| Project | Role | Repository |
| --- | --- | --- |
| **wikiman-frontend** | Vue 3 + Quasar PWA UI | [kendrickkim/wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend) |
| **wikiman-backend** | Node.js + Express + SQLite API (reference) | [kendrickkim/wikiman-backend](https://github.com/kendrickkim/wikiman-backend) |
| **wikiman-backend-php** | PHP backend app (Node API compatible) | [kendrickkim/wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) |
| **wikiman-flutter** | Android·iOS admin WebView + share app | [kendrickkim/wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter) |
| **phastapiv2** | PHP REST framework (PHAST API v2) | [kendrickkim/phastapiv2](https://github.com/kendrickkim/phastapiv2) |

```text
┌─────────────────────────────┐
│      wikiman-flutter        │
│  WebView + share (admin)    │
└──────┬───────────────┬──────┘
       │ WebView       │ /api (login · upload)
       ▼               │
┌──────────────────┐   │
│ wikiman-frontend │   │
│ Vue 3 + Quasar   │   │
└────────┬─────────┘   │
         │ same-origin /api
         └───────┬─────┘
         ┌───────┴────────┐
         ▼                ▼
┌─────────────┐   ┌──────────────────────┐
│  Node path  │   │       PHP path       │
│ wikiman-    │   │ phastapiv2           │
│ backend     │   │   + wikiman-backend- │
│ Express     │   │     php              │
│             │   │ Apache + SQLite/MySQL│
└─────────────┘   └──────────────────────┘
```

The frontend expects **one API contract**. Node and PHP backends match paths,
JSON, permissions, and status codes.

The admin mobile app (`wikiman-flutter`) opens a deployed Wikiman in a WebView
and calls `/api` directly for login and share uploads. It enters the WebView only
after `writer` permission is confirmed.

## Why a PHP path

`wikiman-backend` needs a Node runtime. The PHP path exists so the same wiki can
run where only **Apache + PHP + SQLite** (or MySQL) is available.

- App logic: [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php)
  (domains, filters, JWT, SQLite/MySQL)
- Shared framework: [phastapiv2](https://github.com/kendrickkim/phastapiv2)

PHAST handles routing, attributes, request parsing, filters, and response hooks.
Wikiman-specific rules live in `wikiman-backend-php`.

## Quick start

### A. Local Node

1. Install and run [wikiman-backend](https://github.com/kendrickkim/wikiman-backend)
   (API default port `85`)
2. In [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend), run
   `npm run dev`
3. Browser: `http://localhost:9000`

For deploy, build the frontend as a PWA into the backend `public/` folder:

```bash
# frontend repo
npm run build:backend
# backend repo
npm start
```

### B. PHP hosting

1. Document root must be `wikiman-backend-php/public/`
2. Place [phastapiv2](https://github.com/kendrickkim/phastapiv2) at same-origin
   `/api`
3. Point PHAST `$G_PHASTAPI_CUSTOM_DIR` at [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php)
4. Build the PWA into PHP `public/` while keeping the installer gate:

```bash
# frontend repo (sibling of wikiman-backend-php)
npm run build:php
```

`build:php` copies the PWA into `wikiman-backend-php/public/` and preserves
`index.php`, `install.php`, and `.htaccess`.

5. PHP needs `pdo_sqlite` (or `pdo_mysql`), `mbstring`, `fileinfo`, `curl`,
   `zlib`, and Apache `mod_rewrite`.

Example:

```php
// phastapiv2 config.phastapi.php
$G_PHASTAPI_CUSTOM_DIR = "../wikiman-backend-php";
```

On first visit, `install.php` collects site title, the first admin account, and
SQLite or MySQL settings, then writes `data/config.php` (not web-reachable).
After a healthy install, web reinstall is blocked; a broken config can still be
repaired through the same screen.

The frontend calls the API at `baseURL: '/api'` on the same origin.

## Per-repository roles

### wikiman-frontend

- Vue 3, Quasar, Pinia, PWA
- Site language (한국어 / English) and UI/error i18n (`src/i18n/`)
- Editors: Text / CKEditor / Summernote / TUI / Editor.js / Markdown / HTML
- Service worker does not cache `/api`
- Mobile WebView: `viewport-fit=cover` and CSS
  `env(safe-area-inset-*)` with `--wikiman-safe-*` fallbacks for notch / home
  indicator
- Deploy helpers: `npm run build:backend`, `npm run build:php`

### wikiman-backend

- Express routes, JWT, SQLite (`better-sqlite3`)
- Uploads, backup (`.wkmbak`), link preview, PlantUML
- **Reference implementation** of the API contract

### wikiman-backend-php

- Same paths and responses as the Node API, as a PHAST custom app
- `domain/` + `libs/common/` + permission attributes/filters
- Data: `data/wiki.db` (or MySQL), `data/uploads/`, `data/config.php`
- Web document root: `public/` with installer gate (`index.php`, `install.php`)
- Repository: [kendrickkim/wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php)

### phastapiv2

- Lightweight PHP REST framework
- PHP 8 attributes (`_GET_`, `_POST_`, `_PUT_`, `_PATCH_`, `_DELETE_`)
- IN/OUT filters, multipart / `json_body`, response formatter hooks
- App code isolated in the custom directory
- `/api/docs` UI (search, method filter, dark/light)

See the [phastapiv2 README](https://github.com/kendrickkim/phastapiv2/blob/master/README.md).

### wikiman-flutter

- Flutter Android·iOS admin-only app (display name **Wikiman**)
- Enter Wikiman URL, admin id, and password; enter WebView only after the login
  API confirms `writer`
- Connection details stay in the device secure store and prefill next time
- Return to the connection screen when the web session logs out
- Show **Change connection** in the web user menu only inside the app
- Receive text, images, and files from the Android·iOS share sheet as **Wikiman**,
  upload them, and open the quick-post editor with a Markdown draft
- Match WebView chrome to the site theme (header / page background) and pass
  safe-area insets into the web UI
- App / launcher icons follow the frontend favicon (`tool/generate-app-icons.mjs`)
- Cleartext HTTP allowed for private-network instances; prefer HTTPS publicly
- Shared file size limits follow **Site admin → Attachments → Size limit**
  (enforced on the server too)
- iOS: same Development Team and App Group on Runner and ShareExtension; shared
  files are copied into the App Group before the main app reads them
- Repository: [kendrickkim/wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter)

Details: [wikiman-flutter README (Korean)](https://github.com/kendrickkim/wikiman-flutter/blob/master/README-kr.md)
· [English](https://github.com/kendrickkim/wikiman-flutter/blob/master/README.md)

## Shared API contract

Success responses use Wikiman’s own shape. The PHP path does **not** wrap with
PHAST’s default `{ success, data }`.

```json
{ "posts": [], "total": 0, "page": 1, "pageSize": 10 }
```

Errors:

```json
{ "error": "ERROR_CODE", "params": { } }
```

Stable error codes are returned by the API; the frontend translates them for the
site language.

Auth:

```http
Authorization: Bearer <jwt>
```

Example routes:

- `GET/POST /auth/*`
- `GET/POST/PATCH/DELETE /categories*`
- `GET/POST/PATCH/DELETE /posts*`
- `GET/POST/PATCH/DELETE /quick-posts*`
- `POST /uploads*`, `GET /files/{name}`
- `GET/PATCH /settings`, `GET/PUT /settings/top-menu`
- `GET /link-preview`, `GET|DELETE /link-preview/cache`
- `GET /backup/download`, `POST /backup/inspect|restore`
- `GET|POST /plantuml*`

## Security and operations

- Set a strong `JWT_SECRET` (or PHP `.jwt-secret`) in production.
- Never commit `data/`, `.env`, or uploads to a public repo.
- On the PHP path, link preview and PlantUML block internal (SSRF) targets.
- Restore replaces DB and uploads wholesale; back up current data first.

## License and contribution

Follow each child repository’s LICENSE and contribution rules.
This hub only documents how the Wikiman projects fit together.

## Links

- Frontend: https://github.com/kendrickkim/wikiman-frontend
- Backend (Node): https://github.com/kendrickkim/wikiman-backend
- Backend (PHP): https://github.com/kendrickkim/wikiman-backend-php
- Flutter (admin app): https://github.com/kendrickkim/wikiman-flutter
- PHAST API v2: https://github.com/kendrickkim/phastapiv2
