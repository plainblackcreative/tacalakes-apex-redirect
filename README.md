# tacalakes-apex-redirect

Tiny redirect site for `tacalakes.co.nz` (apex) → `https://www.tacalakes.co.nz/`.

The TACA website lives in a separate repo ([tacalakes-site](https://github.com/plainblackcreative/tacalakes-site)) and is hosted on Cloudflare Pages. The `www` subdomain points there via CNAME.

The apex (`tacalakes.co.nz`) can't CNAME to Pages directly (CNAME isn't allowed at apex per DNS spec), and Cloudflare doesn't publish stable apex A records for sites whose DNS is on an external provider. So this repo exists purely to give us a set of stable apex A records via GitHub Pages.

## How it works

- The site is served by GitHub Pages at `tacalakes.co.nz` (custom domain).
- Every page issues a meta-refresh + JS redirect to `https://www.tacalakes.co.nz`. The JS reads
  `window.location` at runtime, so for a client that runs it, any path/query/hash is preserved —
  `tacalakes.co.nz/anything` lands on `www.tacalakes.co.nz/anything`.
- The meta-refresh is the fallback for clients that don't run JS (some crawlers, link-preview
  bots, curl-based monitors). Unlike the JS, it's a static tag with no access to the request
  path, so it can only redirect to one fixed URL per file. That's why this repo mirrors every
  real page on the main site as its own static file here (`about.html`, `contact.html`,
  `blog/<slug>.html`, etc.) — each one's meta-refresh points at that same page on `www`, so
  no-JS clients land on the right page too, not just the homepage. `404.html` is the only
  exception: it's the catch-all for paths that don't exist on the real site either, so falling
  back to the homepage there is correct.
- A `canonical` link tells crawlers the real URL.
- `robots: noindex` keeps the redirect page itself out of search results.

## DNS records the domain admin (BTG) needs

Four A records on the apex, pointing at GitHub Pages:

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |

GitHub Pages auto-issues a Let's Encrypt cert once DNS resolves; no SSL setup required.

Optionally, GitHub also offers an org-level verified-domain TXT challenge (protects against subdomain-takeover scenarios where DNS gets mis-pointed at GH and someone else claims the domain on Pages). Set up via `github.com/organizations/plainblackcreative/settings/pages` → Add domain. Not required for the site to serve.

## Editing

When a page is added to or removed from the main site (`tacalakes-site`), add or remove the
matching stub file here (same relative path). Copy an existing stub and update the two URLs
(`canonical` href and `http-equiv="refresh"` content) plus the visible link to the new path — the
JS block doesn't need to change, it's identical and generic across every file.

If the redirect target domain itself ever changes, update `index.html` and `404.html`, then all
of the per-page stubs.
