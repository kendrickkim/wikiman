[한국어](README-kr.md)

# Wikiman

A self-hosted personal wiki that also works as a small blog.

Wikiman keeps structured knowledge and chronological publishing in one place.
You can organize posts by category and keyword, keep drafts private, publish a
blog feed, capture notes with Quick Posts, and move the whole site by backing up
one data directory.

This repository is the **project hub**. Source code lives in the repositories
listed below.

## Is Wikiman for you?

Wikiman is designed for one owner rather than a public community.

It may fit if you want:

- A wiki and a personal blog backed by the same posts
- Full control through self-hosting instead of a SaaS account
- SQLite-based operation with straightforward backup and migration
- Several editors, including Markdown, TUI, CKEditor, and Editor.js
- A mobile companion app that sends photos and files to Quick Posts

Only the first registered account becomes the writer. Visitors may read content
that the writer publishes publicly.

## Choose a deployment

The frontend is shared by two backend options:

| Environment | Backend | When to choose it |
| --- | --- | --- |
| Node.js is available | [wikiman-backend](https://github.com/kendrickkim/wikiman-backend) | Reference implementation and simplest deployment |
| Apache or Nginx with PHP | [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) + [phastapiv2](https://github.com/kendrickkim/phastapiv2) | Shared hosting without Node.js; SQLite or MySQL |

Both backends expose the same API to
[wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend).

```text
Android·iOS app ─┐
                 ├─ Wikiman frontend ── /api ── Node backend
Web browser ─────┘                         └───── PHP backend + PHAST
```

## Projects

| Repository | Purpose |
| --- | --- |
| [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend) | Vue 3 + Quasar PWA |
| [wikiman-backend](https://github.com/kendrickkim/wikiman-backend) | Node.js + Express + SQLite API and web host |
| [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) | PHP API and web installer |
| [phastapiv2](https://github.com/kendrickkim/phastapiv2) | REST framework used by the PHP backend |
| [wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter) | Android·iOS admin WebView and share app |

## Quick start with Node.js

```bash
git clone https://github.com/kendrickkim/wikiman-backend.git
git clone https://github.com/kendrickkim/wikiman-frontend.git

cd wikiman-backend
npm install
cp .env.example .env
npm run dev

# in another terminal
cd ../wikiman-frontend
npm install
npm run dev
```

Open `http://localhost:9000`. The first account you create becomes the writer.

For production, build the PWA into the backend and start its web host:

```bash
# wikiman-frontend
npm run build:backend

# wikiman-backend
npm start
```

See the [Node backend README](https://github.com/kendrickkim/wikiman-backend)
for environment variables and reverse-proxy setup.

## Quick start on PHP hosting

The PHP route needs three projects:

1. Build [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend)
   with `npm run build:php`
2. Use `wikiman-backend-php/public/` as the Apache or Nginx document root
3. Expose [phastapiv2](https://github.com/kendrickkim/phastapiv2) at `/api` and
   point its custom app directory to `wikiman-backend-php`
4. Open the site and complete the browser installer

The installer creates the first administrator and configures SQLite or
MySQL/MariaDB. See the
[PHP backend README](https://github.com/kendrickkim/wikiman-backend-php) for
the complete Apache/Nginx layout and security settings.

## What you get

- Categories, keywords, search, drafts, publishing, and trash
- Wiki home page or chronological blog mode
- Text, rich-text, structured, Markdown, and HTML editors
- Quick Posts that can later become full posts
- Attachments, protected files, and configurable upload limits
- PlantUML, code highlighting, line numbers, and copy buttons
- Link previews with server-side request protection
- Korean and English site UI
- PWA support and server-generated social sharing metadata
- Backup and restore for SQLite deployments

## Mobile app

[wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter) is an optional
admin companion app for Android and iOS. It opens your site in a WebView after
confirming writer access, and receives text, photos, and files from the system
share sheet as Quick Post drafts.

The app is useful after a Wikiman site is already running; it does not include
the frontend or backend itself.

## Operations at a glance

- Keep `.env`, database files, uploads, and PHP `data/config.php` out of Git
- Use a strong JWT secret in production
- Use HTTPS for every public deployment
- Back up the complete data directory, not only the database
- On MySQL/MariaDB, use provider snapshots or `mysqldump`; `.wkmbak` currently
  covers SQLite deployments

Each repository README contains the details relevant to that component.
