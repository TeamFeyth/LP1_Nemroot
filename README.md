# Nemroot — Landing Page 1 (Astro)

## Copy policy for this repo

**Every user-visible string on this page is reproduced verbatim from Section 5 of
the build spec** (`Nemroot_Landing_Pages.md` / `.pdf`). Nothing has been reworded,
shortened, expanded or "improved". The HTML draft (`lp1-html.html`) was used only
as the reference for layout, spacing and visual design — where the draft's wording
differs from the spec, **the spec wins**.

If the copy needs to change, change it in the spec first, then mirror it here.
`src/pages/index.astro` holds all page copy in one place with comments mapping each
block back to its spec section number.

Two things reproduced exactly as written in the source document, flagged here so
nobody thinks they are build errors:

- Bullet 2 reads `Car.com,SMS` (missing space after the comma).
- Bullet 6 reads `Live in 24 to 48 hours,.` (stray comma before the period).

## Stack

Astro, static output. Ships plain HTML/CSS/JS — no adapter needed for Cloudflare Pages.

## Local development

```bash
npm install
npm run dev       # http://localhost:4321
npm run build     # outputs to /dist
npm run preview
```

## Deploying to Cloudflare Pages

- **Build command:** `npm run build`
- **Build output directory:** `dist`
- **Framework preset:** Astro

No environment variables required for the current build.

## Project structure

```
src/
  layouts/BaseLayout.astro   <head>, fonts, Meta Pixel, tracking placeholders
  components/                one component per spec section
  scripts/interactions.js    popup, progressive reveal, phone formatting, validation
  styles/global.css          design tokens + base elements
  pages/index.astro          ALL PAGE COPY LIVES HERE
```

## What's implemented

- **Meta Pixel** (`2302599970275699`) in `<head>`, `PageView` on load, `Lead` on submit.
- **Popup** — desktop exit-intent + mobile scroll-depth (>65%), progressive field
  reveal (seats → leads + platforms → name/phone/dealership), and a
  **show-once-per-session** rule. This closes the build gap the spec flags in
  Section 5 / 4. Manual "Book a Demo" clicks always open it.
- **Form** — exactly the six fields the spec lists, in spec order. No extra fields.
- **Hidden fields** for the CRM handoff: `form_source` (hero/bottom/popup),
  `page_url`, `timestamp`, `utm_source/medium/campaign/content/term`.
- Footer phone is a `tel:` link (spec Section 5 / 2, and Open Item #7).
- Phone inputs auto-format to `(XXX) XXX-XXXX` as the user types.

## ⚠️ Open Items — unresolved, carried over from the spec

1. **Confirmation copy and thank-you URL** (spec: "Success Heading / Success Body:
   blank — not specified"). The form currently shows a minimal placeholder,
   `Thank you. We'll be in touch shortly.`, marked with a comment in
   `LeadForm.astro`. **Replace this with approved copy before launch.**
2. **No SMS/call consent language on any form despite collecting phone numbers.**
   The spec's own note says consent language "needs to be added and approved" —
   it has not been written or approved, so nothing has been put on the form. This
   is a legal/TCPA exposure that should be resolved before paid traffic runs.
3. Required-field rules and phone validation are "not specified" in the spec. The
   current build requires all six fields and a 10-digit phone. Confirm.
4. Privacy Policy, Terms of Service and Sitemap links have no destinations (`#`).
5. **CRM endpoint** — `sendLeadToCRM()` in `interactions.js` is a no-op stub.
   Submissions are not stored anywhere yet.
6. GTM / GA4 / CallRail / Search Console IDs — placeholders are commented into
   `BaseLayout.astro` at the correct placement. Drop the real IDs in and uncomment.

## Meta Conversions API token

The build doc included a Meta Conversions API **access token**. That is a
server-side secret and this project is fully static, so anything in this repo is
publicly visible in the browser. **The token is not in this project.** When the
CRM/CAPI integration is built, store it as an encrypted Cloudflare Pages
environment variable and call the Conversions API from a Pages Function.
