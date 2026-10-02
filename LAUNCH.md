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
  - `/category/マイク/page/5/` → redirect to `/categories/マイククインシー/page/5/`.
  - an old image URL, e.g. `/wp-content/uploads/2024/11/M.jpg` → 200.
  - `/sitemap_index.xml` → sitemap index pointing at `/sitemap.xml`.
  - `/feed/` → RSS.

## 4. Google Search Console
- Property `www.despertando-nihongo.com` (or the domain property) — verified via DNS TXT at Infomaniak if not already.
- Sitemaps: add `https://www.despertando-nihongo.com/sitemap.xml`; remove the old `sitemap_index.xml`.
- URL inspection → Request indexing for the home page and two or three recent posts.
- Watch Coverage / Pages report for a couple of weeks: expect ~563 URLs reported as "redirect" (471 `/tag/…`, 9 `/category/…`, 82 archive pagination, the author page), nothing as 404.

## 5. Hostinger
The old site is already unreachable (HTTPS fails the TLS handshake, HTTP returns 403) as of 2026-10-02, the plan's expiry date — so there is nothing left to keep running and no reason to delay the DNS switch. The full backup is in the private `hostinger-backup` repo; the 2026-09-18 database dump there is the inventory of record for the old URL set (the last post published on WordPress was 2026-09-15).

## What was done for SEO parity (reference)

Verified by rebuilding with the production baseURL and diffing **1,913 old URLs** — the Yoast
sitemaps plus the URL classes they omit — against the build. Result: 1,349 served directly,
563 via redirect, **1 missing** (`/wp-content/uploads/2024/05/B-296x300.jpg`, which is absent
from the Hostinger backup too, so it was already a 404 on WordPress).

- All 1,166 URLs from the old Yoast sitemaps resolve: 684 posts at identical paths, 9 categories + 471 tags + author page via redirect (alias) pages.
- Media keeps its old WordPress path: files live in `static/wp-content/uploads/`, so every
  `/wp-content/uploads/…` URL still resolves. 591 of the 592 media URLs the old site served
  (post bodies + attachment originals) are present, including 60 unreferenced originals and
  3 files recovered from LiteSpeed's `.bk` copies. WordPress's unused sized variants
  (~2,270 files, 440 MB) are deliberately not carried over.
- Old archive pagination redirects: 82 stubs in `static/category/…/page/N/` and
  `static/tag/…/page/N/` map to the matching new paginated page. The three multi-page series
  tags point at the equivalent category page rather than a single post.
- Legacy Yoast sitemap URLs (`/sitemap_index.xml`, `/post-sitemap.xml`, `/category-sitemap.xml`,
  `/post_tag-sitemap.xml`, `/author-sitemap.xml`) return a sitemap index pointing at `/sitemap.xml`.
- Titles keep the old format (`<post> - デスペルタンド 日本語`, home `デスペルタンド 日本語 - 光のメッセージ`, paginated `… - Page N of M`).
- `lang="ja"` (old site wrongly said `en-US`), meta descriptions on every post (old site had them on 34), `lastmod` in the sitemap, self-canonical paginated pages, robots `max-image-preview:large`, Open Graph / Twitter / JSON-LD via PaperMod, same cover images.
- RSS stays at `/feed/`.
- Body text is character-identical to the old site (checked on a sample); pagination is still 10 posts per page at `/page/N/`.
- Not carried over: the 471 tag archive pages themselves (they were one-post title duplicates),
  the `/comments/feed/` and per-post `/<slug>/feed/` feeds (comments are disabled; feeds are not indexed),
  and WordPress's unused generated image sizes.
- Cannot be carried over on static hosting: `/?p=<ID>` shortlinks (query strings are ignored, so
  they serve the home page rather than 404). Redirects are meta-refresh, not 301 — the only
  mechanism GitHub Pages offers.
