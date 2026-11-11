# Forum

Flarum-style forum on Vite + React, Hono on Cloudflare Workers, D1 for data.
v1 includes tags, discussions/posts, BBCode, reactions, edit history, pin/lock, roles.

## Setup
    npm install
    npx wrangler d1 create forum          # paste the database_id into wrangler.jsonc
    npm run db:local                      # apply migrations locally
    npm run dev                           # http://localhost:5173
    npm run db:remote && npm run deploy   # production

The first account you register becomes the Admin.

## Upgrading from the first zip
Copy these over your project, then run `npm install` (no new packages) and `npm run db:local` / `npm run db:remote`.
Migration `0002_permissions.sql` only adds tables and rows, so existing data is untouched.

## Phase 2 additions
- **Per-tag permissions** (View / Start discussions / Reply) for Members, and View for Guests.
  Admins and Moderators always have everything. Edit them at `/admin` (admin accounts only).
  A tag a group can't view is hidden everywhere: tag list, discussion list, thread pages, sitemap, SSR.
- **Server-rendered pages** for `/`, `/t/:slug`, `/d/:id` with title, description, canonical URL,
  Open Graph tags and JSON-LD, plus `/sitemap.xml` and `/robots.txt`. Rendered as a Guest, so private tags never leak.
  Browsers load the React app on top of it (it replaces the server HTML rather than hydrating it).
- **Rate limiting**: 10 login attempts per 15 minutes and 5 registrations per hour, per IP (stored in D1).
- **Edit history** viewer for post authors and moderators.
- `SITE_NAME` in `wrangler.jsonc` sets the name used in page titles.

## Known gaps (next up)
- Search (D1 FTS5), email verification and password reset, avatars (R2), private messages, notifications.
- Pagination for long threads (currently first 200 posts) and for the discussion list (first 50).
- Reaction counts are computed per request; denormalize if threads get large.
- Server HTML is not cached; add Cache-Control or the Cache API if crawlers load you heavily.
