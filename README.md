# Backyard Diggers — Website

A single-page marketing & bookings site for Backyard Diggers, the RC construction
play & party venue in Weltevreden Park, Johannesburg.

## Deploy to Netlify (easiest)
1. Go to https://app.netlify.com/drop
2. Drag the **entire `backyard-diggers` folder** onto the page.
3. Done — Netlify gives you a live URL in seconds.
4. (Optional) In Site settings → Domain, connect `backyarddiggers.co.za`.

No build step is needed — it's plain HTML/CSS/JS.

## What's where
- `index.html` — the whole site (CSS + JS are inline).
- `assets/img/` — optimised photos, logo (transparent), favicons, social-share image.
- `assets/video/diggers-clip.mp4` — compressed action clip (loads only when tapped).
- `robots.txt`, `sitemap.xml` — for Google search.

## Bookings
Every "Book" button points to your Facebook page:
https://www.facebook.com/profile.php?id=61589118839099
WhatsApp links go to 073 257 1112. To change either, search the file for the
Facebook URL / `wa.me/27732571112` and replace.

## Easy edits later
- **Prices:** search `R150` in `index.html`.
- **Phone / email:** search `27732571112` and `info@backyarddiggers.co.za`.
- **Add gallery photos:** drop a new image in `assets/img/`, then copy a
  `<figure>…</figure>` block inside `id="galGrid"` and point it at the new file.
- **Map:** the Google Maps embed uses the suburb. Once you're happy to publish an
  exact pin, replace the `src` of the `<iframe class>` / map link with your map link.

## SEO note
Update the canonical URL / `og:url` / sitemap from `backyarddiggers.co.za` to your
real domain once it's live, then submit the sitemap in Google Search Console and
link the site to your Google Business Profile.
