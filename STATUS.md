# Status — backyard-diggers
_Updated: 2026-07-15_

## Google Reviews — live, self-rendered from Featurable's data API (2026-07-15)
Reviews section is no longer manual-paste and no longer embeds Featurable's
official widget either — we render it ourselves now. History: Jay signed up
at featurable.com (free plan, Google sign-in to the GBP), created widget id
`e52b10e1-f89a-4033-8fff-e53568452e0f`. First attempt embedded their widget
directly, but hit two dead ends:
1. Widget renders inside an **open shadow root** — confirmed via Playwright —
   so our site CSS structurally cannot restyle the review cards (shadow DOM
   blocks outside styles by design). That's why Jay couldn't find "the CSS":
   it doesn't exist in our codebase, full stop.
2. Featurable's dashboard has **no minimum-rating filter** — confirmed by
   inspecting the widget's own config via its public API (other unset fields
   still appear as `null`, but there's no rating-filter key at all, on any
   plan tier we could find). Jay wasn't missing a button; it isn't there.

**Fix:** `https://api.featurable.com/v2/widgets/<uuid>` is a public,
unauthenticated GET — the same JSON their embed.js fetches to render, just
consumed directly by us instead. `loadReviews()` (end of index.html) fetches
it client-side on page load, filters to `rating >= 4`, sorts best+newest
first, caps at 10, and renders into `.review-grid`/`.review-card` (restored
— same dark-theme classes the original manual system used). Reviewer
surnames are shortened to an initial ourselves ("Marco Mariska" -> "Marco M.")
since Featurable's widget did that automatically and our direct fetch bypasses
it — this was privacy-sensitive, don't skip it if this code is ever touched.
The "Read all our Google reviews" button uses the real Google Maps
write-a-review link from `gbpLocationSummary.writeAReviewUri` in the API
response (found via that same endpoint) — better than Featurable's own
redirect-wrapped version, and possibly reusable for the QR-code-on-venue idea
Jay mentioned earlier, still not done.

**Risk, noted deliberately:** this endpoint isn't officially documented for
third-party use — it could change shape or get locked down without notice.
If reviews silently stop appearing, check this first before assuming a code
regression. `loadReviews()` fails closed (section stays hidden, console.error
only) rather than breaking the page.

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
