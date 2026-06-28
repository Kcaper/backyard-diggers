# Status — backyard-diggers
_Updated: 2026-06-28_

## SEO pass (2026-06-28) — all 7 loops closed
1. Images -> WebP: 7.2MB -> 2.7MB (~62% lighter). og-image + video-poster stay JPG.
2. _headers: static assets cache 1yr; HTML must-revalidate.
3. Meta description trimmed 287 -> 161 chars.
4. Title trimmed 85 -> 57 chars.
5. _redirects: raw netlify.app host 301 -> custom domain.
6. Schema enriched: geo coords, hasMap, slogan; postcode left out (unconfirmed).
7. OG tags completed (image dims/alt, site_name, locale en_ZA); no Twitter (not a channel).
Note: if swapping an image, use a NEW filename (1yr cache). Postcode still TBC.
Re-index requested in Search Console 2026-06-28 (after title/desc/image/schema changes).

## Latest changes (2026-06-28)
- Ages: now "4+" with no upper limit (meta, schema, stats, pricing, FAQ).
- Adult pricing reworked: parents play FREE with their kids; adult-only events
  (team building/corporate) are paid per group. "Dads drive free — moms too".
  Removed "watching is free".
- "Staff help with controls" -> "RC trainers on site".
- Video section: added 3 more clips (576x1024) with a thumbnail switcher.
- Booking flow: WhatsApp panel now ABOVE the form; form is secondary.

## Now
Big review-pass shipped + Google presence being set up. Working through
Google Business Profile (last big piece).

## Done today
- Site update: founder story, "what happens when you arrive", on-site booking
  form (Netlify Forms), WhatsApp buttons + floating button, full address
  (6 Platboom Avenue), facilities, dads-drive-free, reworked birthday/pricing,
  hidden reviews section.
- SEO: absolute og:image, sitemap lastmod.
- Favicon fix: added 48/96px PNGs + root favicon.ico (Google needs >=48px;
  old 32px was being ignored). Live on site.
- Google Workspace: info@backyarddiggers.co.za created + branded Gmail signature
  (email-signature.html, Kathy Tomlinson) loaded into Gmail.
- Google Search Console: domain property VERIFIED (existing TXT did it),
  sitemap.xml submitted (Success, 1 page), homepage "Request indexing" done.

## Next
- [ ] Finish Google Business Profile (in progress) — categories, address,
      hours, description (saved below), photos (cover = kids-playing-17.jpg)
- [ ] GBP postcard verification (~5 days) — then upload 8-10 photos
- [x] Booking form emails — DONE: enabled form detection (ignore_html_forms was true),
      form registered, email notification -> info@backyarddiggers.co.za, end-to-end
      test submission recorded by Netlify. (Verify the test email landed / not in spam.)
- [ ] Add Google reviews link + real reviews to BD_REVIEWS in index.html once 3+ exist
- [ ] Favicon + fresh snippet will appear in Google after next crawl (few days)

## Blocked / waiting
- Google Business Profile: SET UP, awaiting verification (~by 2026-07-02, postcard).
  Profile not public until verified. Reviews link only available after verification.
- Confirm which Google account owns the GBP (should be info@backyarddiggers.co.za).
- Google reviews link (needs verified GBP)
- GBP photo bundle ready at ~/Websites/2026/BackyardDiggers/Google-Business-Photos (15 files)
- Hours: GBP set with different hours than site — update site to match once user provides them.

## GBP description (final, 519 chars, playful)
Real diggers. Little drivers. Big fun. At Backyard Diggers, kids aged 3+ take
the controls of real remote-control excavators and dump trucks — scooping,
hauling and racing across a multi-level dig site. Hard hats on, hi-vis up, our
crew on hand to help. It's all undercover, so rain or shine the dig is on.
Parents relax in the shade with tea, coffee and cold drinks — and dads always
get dragged in too! Birthday parties, play sessions, school groups, team
building and treasure hunts. Only the kids pay. Come dig in!

## Decisions / notes
- Live URL: https://inquisitive-beijinho-f1af31.netlify.app (custom domain: backyarddiggers.co.za, www canonical)
- Deploy: push to `main` → GitHub Action → Netlify (~90s)
- Workspace/owner Google account: info@backyarddiggers.co.za
- Bookings via on-site Netlify form + WhatsApp 073 257 1112 (no longer Facebook)
- Contact: Kathy Tomlinson
- Hard hats & hi-vis = "provided to wear on site" (not paid/kept)
- Reviews: NO fabricated reviews — hidden until real Google ones added
- Party size: ideal 8-10 kids. Ages 3+. Only kids pay; adults/dads/watching free.
- Theme: yellow hazard / construction (Anton + Saira Condensed + Hanken Grotesk)
