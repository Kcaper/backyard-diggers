# Status — backyard-diggers
_Updated: 2026-07-15_

## SEO/performance audit + fixes (2026-07-15)
Ran a real Lighthouse audit (not just manual read-through) against the live
site, verified every finding by hand, then fixed what was fixable. Starting
scores: Performance 44, Accessibility 97, Best Practices 100, SEO 92.

**Shipped fixes** (commits `590392f`, `d68230d`):
- Logo: 811x834 PNG @ 260KB → 200x206 WebP @ 18KB (was displayed at 85x88px).
  `logo.png` itself kept in place, unchanged — still referenced live in the
  emailed Gmail signature (`email-signature.html`), do not delete it.
- About-section photo: added a 700w responsive `srcset` variant for mobile.
- Google Fonts: non-blocking load (preload+swap-on-load pattern), and
  `display=optional` instead of `swap` — **this was an iterative fix**, see
  below, don't revert to `swap` without understanding why.
- Fixed 2 heading-order skips (h2→h4 in Visit section, h2→h5 in footer),
  both bumped to h3 to match the site's existing nesting convention.
- Added a `<main>` landmark around the primary content.
- Bumped `sitemap.xml` lastmod to today.

**Caught and fixed before shipping — a real regression, not just Lighthouse
noise:** adding `width`/`height` attributes to images without also pairing
the right `width:auto`/`height:auto` in CSS visibly stretched 3 images
(footer logo, about-photo, one gallery photo). Root cause: a browser only
auto-derives the missing dimension from HTML width/height attributes when
the *other* dimension's CSS value is genuinely `auto` — `.foot-brand img`
had `height:78px` but no `width:auto`, so the browser used the width
*attribute* (200) literally instead of scaling it, stretching the logo.
Caught via direct `getBoundingClientRect()` measurement (not just trusting
Lighthouse), fixed by adding the missing `auto` pairing in 3 CSS rules
(`.foot-brand img`, `.about-photo img`, `.gal-grid img`).

**The font CLS fix took two tries — worth understanding why:**
1st attempt (non-blocking font load with `display=swap`) fixed First
Contentful Paint but made CLS *worse* on live (0.297→0.327) — un-blocking
render meant text now paints with a fallback font immediately, then visibly
reflows when the real font swaps in. Locally (near-zero latency) this
measured as CLS:0, which was misleading — the swap gap is latency-dependent
and only showed up under real network conditions. Switched to
`display=optional`: if the font isn't cached within ~100ms, the browser
keeps the fallback for that whole page view instead of swapping in later.
Confirmed CLS 0 on the **live** site after this (not just localhost).
**Lesson for next time: always re-verify perf/CLS fixes against the live
URL, not just localhost — localhost's near-zero latency can hide exactly
the failure mode you're trying to fix.**

**Final live-site scores (Lighthouse, this session):** Accessibility 100,
Best Practices 100, SEO 92 (one point is a false-positive robots.txt check —
verified independently via direct curl, robots.txt serves fine), Performance
61 (up from 44). CLS confirmed 0.

**Known limitation, not a site bug:** raw Performance/LCP numbers from this
session's testing environment are skewed by its own network path to Netlify
— measured TTFB ~1.1-1.4s and ~60-90KB/s throughput to backyarddiggers.co.za,
vs 22Mbps to Cloudflare from the same sandbox in the same session. A local
(near-zero-latency) test of the identical fixed code scored Performance 99,
LCP 2.0s. Real visitors (mostly South African, normal connections) almost
certainly see something much closer to that. If performance ever needs
re-checking, use Google PageSpeed Insights (real field data from actual
visitors) rather than re-running Lighthouse from an unknown sandbox network
path — it's a more trustworthy source for the absolute number.

**Not fixed, needs a decision from Jay/Kathy, not a code change:** Google
Analytics/Tag Manager (`G-ZV3K1EGKNW`) is running on the site. Wasn't set up
in any Claude session — confirm it's intentional and actually theirs.

## Google Reviews — live via Featurable's official widget, final (2026-07-15)
Settled here after trying three approaches same day — worth knowing the
history so nobody re-litigates it:
1. Manual-paste `BD_REVIEWS` array (original system) — replaced, not real-time.
2. Featurable's official embed, framed in `.reviews-widget-frame` (white
   card, yellow top border, since the widget renders in an **open shadow
   root** — confirmed via Playwright — so our site CSS structurally cannot
   restyle the cards inside it). Hit a wall: Featurable's dashboard has **no
   minimum-rating filter**, confirmed by inspecting the widget's own config
   via its public API (other unset fields still show as `null`; there's no
   rating-filter key at all). A 4★ review showed through.
3. Self-rendered from Featurable's public data API directly (bypassing their
   embed entirely) — solved both the theming and the filtering, but Jay
   found the resulting cards "a bit fake" despite matching the site's colors
   (no profile photos, no Google branding, no relative timestamps — the
   things that read as *authentic* rather than *on-brand*).

**Landed on: back to (2), Featurable's official embed**, framed. Jay
explicitly chose authenticity over strict rating filtering — the one 4★
review is accepted as a tradeoff. Still fetches live on every page load
(confirmed: same freshness whether embedded or self-rendered — new reviews
appear once Featurable's backend syncs from Google, typically within a day).
Widget id `e52b10e1-f89a-4033-8fff-e53568452e0f` (Jay's featurable.com
account, free plan, connected via Google sign-in to the GBP).

**Update, same day:** Jay found Featurable's own dashboard styling controls
(a `color_scheme` toggle and a `custom_css` textarea — both real fields,
confirmed earlier via the widget's API config) and switched color_scheme to
dark himself. That's a *supported* customization path, distinct from our
site's own CSS — Featurable injects it directly into the widget's shadow
root, which is why it can reach where our stylesheet can't. Gave Jay a
brand-matching custom CSS block (card bg `#262017`, text `#F4EDDE`/`#ddd4c2`,
star fill `#FFC21E`, button recolored to brand yellow, `Hanken Grotesk` font)
to paste into that field — verified first by injecting the identical CSS
into the live shadow root via Playwright so it was confirmed working before
handing it over, not guessed. Real class names came from `data-featurable-classname`
attributes Featurable puts on every element (their own documented styling
hooks) — grep the shadow DOM for that attribute if this needs revisiting.
Removed our `.reviews-widget-frame` white wrapper from index.html since the
widget now dark-themes itself; its outer containers are transparent so the
page's own dark background shows through the gaps.

**If reviews-widget work resumes:** `https://api.featurable.com/v2/widgets/<uuid>`
is a public, unauthenticated GET returning the same JSON the embed fetches —
useful for diagnostics — but don't reach for it as a widget replacement again
without asking first; that door's been tried and closed.

**Brand-match custom CSS: DONE (2026-07-15).** Jay pasted the CSS block into
Featurable's dashboard custom_css field — reviews widget now fully matches
site colors/font, not just the color_scheme:dark toggle from earlier.

**QR code for on-venue reviews: DONE (2026-07-15).** Jay already had one —
official Google-generated code (`g.page/r/CRL4gT7VS1yuEBM/review`, tagged
`utm_source=gbp`), not something I generated. Saved to
`../marketing/qr-google-review.png` (sibling folder, not in this git repo)
and wired into `../marketing/01-review-request-whatsapp.md`'s `[REVIEW LINK]`
placeholders. Marketing folder isn't version-controlled — plain files on disk.

## Party Quote Builder (2026-07-15)
Added a self-service popup wizard (triggered by the "Get a Party Quote" button
in the Pricing section) so clients can build their own quote line-by-line and
see a live total, no back-and-forth needed:
- Venue: Option A (R1,200/1hr, 1–10 kids) or Option B (R2,000/2hrs, 1–10 kids),
  max 15 pax — last-minute additions above 10 auto-calc at R120/child/hour.
- Add-ons: Party Decor (R900 flat), food platter (price-on-request, shown but
  not added to the numeric total), party pack (R50/child) or deluxe (R100/child).
- Ends in a prefilled WhatsApp message summarizing the build + total, sent to
  073 257 1112 — matches the site's existing WhatsApp-first booking pattern.
Tested (Playwright): math correct across combos, mobile layout holds up,
backdrop/X/Escape all close it, no console errors.
If BYDW pricing ever changes, the numbers live inline in the modal HTML/JS
near `id="quoteModal"` in index.html — no separate config file.

## Loop 8 SEO (2026-07-15) — keyword-intent content expansion
Jay sent a ~250-term keyword wishlist (family day out, birthday party, RC/digger
theme, audience segments like grandkids/homeschool/crèche, local intent for
Roodepoort/Randburg/Gauteng). Explained meta-keywords stuffing is dead/useless —
instead wove real intent into visible content:
- Added FAQ: "Who is Backyard Diggers good for?" (siblings/cousins/grandparents/
  mommy-daddy-and-me/homeschool/crèche — all true, no invented claims).
- Added FAQ: "Do you only cater to Weltevreden Park?" (Roodepoort/Randburg/Joburg draw).
- Expanded "What if it rains?" + schools/events FAQ answers with school-holiday/
  homeschool/crèche language.
- Added FAQPage JSON-LD (didn't exist before) mirroring all 10 visible FAQ Q&As —
  eligible for rich results / AI overviews on these exact question phrases.
- Schema areaServed: added Randburg, Gauteng (was Joburg/Roodepoort/Weltevreden Park only).
Deliberately did NOT touch title/meta description (already trimmed in Loop 3/4) or
add unsubstantiated superlative claims ("best", "top-rated", "award-winning") —
those should be earned via real reviews, ties to the pending reviews-widget work.

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
- [ ] Auto-pull Google reviews (deferred 2026-07-14) — want up to 10 reviews,
      4.5★+ only, auto-refreshing. Decision: use a third-party review widget
      (e.g. Elfsight/EmbedSocial) connected via "Sign in with Google" —
      Google's own Places API caps at 5 reviews and can't filter by rating.
      No Google Cloud project/API key exists for this site. Until then,
      BD_REVIEWS in index.html stays manual (paste-in).
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
- Hours: DONE — site now matches GBP (Mon-Fri 9-19, Sat-Sun 7-21, public hols with weekend).

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
