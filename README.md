# tacalakes-apex-redirect

Tiny redirect site for `tacalakes.co.nz` (apex) → `https://www.tacalakes.co.nz/`.

The TACA website lives in a separate repo ([tacalakes-site](https://github.com/plainblackcreative/tacalakes-site)) and is hosted on Cloudflare Pages. The `www` subdomain points there via CNAME.

The apex (`tacalakes.co.nz`) can't CNAME to Pages directly (CNAME isn't allowed at apex per DNS spec), and Cloudflare doesn't publish stable apex A records for sites whose DNS is on an external provider. So this repo exists purely to give us a set of stable apex A records via GitHub Pages.

## How it works

- The site is served by GitHub Pages at `tacalakes.co.nz` (custom domain).
- Both `index.html` and `404.html` issue a meta-refresh + JS redirect to `https://www.tacalakes.co.nz`, preserving the path, query, and hash. So `tacalakes.co.nz/anything` lands on `www.tacalakes.co.nz/anything`.
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

This repo should almost never need editing. If you change the redirect target, update both `index.html` and `404.html`.
