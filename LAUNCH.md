# Launch checklist — moving www.despertando-nihongo.com to GitHub Pages

Everything below is already in place in the repo; this is the order of operations for the switch.
Until the switch, the github.io copy sends `noindex` + `robots.txt Disallow: /` so Google never sees it as a duplicate.
That flips off automatically as soon as the site is built with the real domain as base URL.

## 1. GitHub Pages
1. Repo → Settings → Pages → Custom domain: `www.despertando-nihongo.com` → Save (creates the `CNAME` file).
2. Wait for the DNS check to pass, then tick **Enforce HTTPS** (certificate is issued automatically).

## 2. DNS at Infomaniak (domain zone for despertando-nihongo.com)
| Type  | Name | Value |
|-------|------|-------|
| CNAME | www  | `despertando-nihongo.github.io` |
| A     | @    | `185.199.108.153` |
| A     | @    | `185.199.109.153` |
| A     | @    | `185.199.110.153` |
| A     | @    | `185.199.111.153` |

Remove the old A / CNAME records that point at Hostinger. Keep MX / TXT records untouched.
GitHub redirects the apex (`despertando-nihongo.com`) to `www` automatically, same as WordPress did — `www` stays the canonical host.

## 3. Repo
- `hugo.yaml`: set `baseURL: https://www.despertando-nihongo.com/` and push. (The Actions build already uses the Pages URL, so canonicals switch even before this — but local builds need it.)
- After the deploy, verify:
  - `https://www.despertando-nihongo.com/` → 200, `<link rel=canonical>` on www, **no** `noindex` meta, `robots.txt` shows `Disallow:` (empty) + sitemap.
  - `https://despertando-nihongo.com/` → redirects to www.
  - an old post URL, e.g. `/天使　２０２０年３月２１日　angels-via-ann-albers/` → 200.
  - `/category/ブロッサム/` and any `/tag/…/` URL → redirect page to the new location.
  - `/feed/` → RSS.

## 4. Google Search Console
- Property `www.despertando-nihongo.com` (or the domain property) — verified via DNS TXT at Infomaniak if not already.
- Sitemaps: add `https://www.despertando-nihongo.com/sitemap.xml`; remove the old `sitemap_index.xml`.
- URL inspection → Request indexing for the home page and two or three recent posts.
- Watch Coverage / Pages report for a couple of weeks: expect the 471 `/tag/…` URLs to be reported as "redirect", nothing as 404.

## 5. Hostinger
Leave the WordPress site running until DNS has switched and the checks above pass. Then let the plan expire (2026-10-02). The full backup is in the private `hostinger-backup` repo.

## What was done for SEO parity (reference)
- All 1,166 URLs from the old Yoast sitemaps resolve: 684 posts at identical paths, 9 categories + 471 tags + author page via redirect (alias) pages.
- Titles keep the old format (`<post> - デスペルタンド 日本語`, home `デスペルタンド 日本語 - 光のメッセージ`, paginated `… - Page N of M`).
- `lang="ja"` (old site wrongly said `en-US`), meta descriptions on every post (old site had them on 34), `lastmod` in the sitemap, self-canonical paginated pages, robots `max-image-preview:large`, Open Graph / Twitter / JSON-LD via PaperMod, same cover images.
- RSS stays at `/feed/`.
- Body text is character-identical to the old site (checked on a sample); pagination is still 10 posts per page at `/page/N/`.
- Not carried over: the 471 tag archive pages themselves (they were one-post title duplicates), the `/comments/feed/` (comments are disabled).
