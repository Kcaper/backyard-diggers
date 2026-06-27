# Status — backyard-diggers
_Updated: 2026-06-27_

## Now
Big review-pass update shipped: founder story, "what happens when you arrive"
section, on-site booking form (Netlify Forms), big green WhatsApp buttons,
full public address (6 Platboom Avenue), facilities (secure venue, bathrooms,
child minders, tea/coffee, staff help with controls), dads-drive-free messaging,
reworked birthday + pricing sections, and a reviews section (hidden until real
Google reviews are added).

## Next
- [ ] Client: in Netlify dashboard → Forms → enable email notifications to info@backyarddiggers.co.za
- [ ] Add Google reviews link + real reviews into the script in index.html (BD_REVIEWS / BD_GOOGLE_REVIEWS_URL) once 3+ exist
- [ ] Submit one test booking after deploy to confirm Netlify captures the "booking" form
- [ ] Optional: confirm play-session group-size wording (currently parties "ideal 8–10")

## Blocked / waiting
- Google Business Profile reviews link (client to provide)

## Decisions / notes
- Live URL: https://inquisitive-beijinho-f1af31.netlify.app (custom domain: backyarddiggers.co.za)
- Deploy: push to `main` → GitHub Action → Netlify (~90s)
- Bookings now via on-site Netlify form (no longer Facebook) + WhatsApp 073 257 1112
- Hard hats & hi-vis worded as "provided to wear on site" (not a paid/kept item)
- Reviews: NO fabricated reviews — section stays hidden until real Google ones are added
- Theme: yellow hazard / construction look (Anton + Saira Condensed + Hanken Grotesk)
