# Nemroot — Landing Page 1 (Astro)

"Every Lead Answered in 14 Seconds" — direct value-prop landing page.

## Stack

Astro (static output, no adapter needed) — ships as plain HTML/CSS/JS, works
out of the box on Cloudflare Pages.

## Local development

```bash
npm install
npm run dev       # http://localhost:4321
npm run build     # outputs to /dist
npm run preview   # serve the production build locally
```

## Deploying to Cloudflare Pages

Connect this repo in Cloudflare Pages and use:

- **Build command:** `npm run build`
- **Build output directory:** `dist`
- **Framework preset:** Astro

No environment variables are required for the current build.

## Project structure

```
src/
  layouts/BaseLayout.astro   <head> boilerplate, fonts, Meta Pixel, tracking placeholders
  components/                one component per page section (see Section 5 of the build spec)
  scripts/interactions.js    popup, progressive reveal, phone formatting, validation, submit
  styles/global.css          shared design tokens (color/type/radius) + base elements
  pages/index.astro          assembles the page
```

## What's already wired up

- **Meta Pixel** (`2302599970275699`) — loaded in `<head>`, fires `PageView` on load and a
  `Lead` event on every successful form submit (hero / bottom / popup).
- **Popup triggers** — desktop exit-intent + mobile scroll-depth (>65%), each firing **at most
  once per browser session** (`sessionStorage`). Manual "Book a Demo" clicks always open it
  regardless of session state. This closes the gap flagged in the build doc for this page.
- **Progressive reveal in the popup** — seats → leads + ad platforms → name/phone/dealership,
  per the spec. (Not applied to the hero/bottom forms — the spec only calls for it in the popup.)
- **TCPA/SMS consent checkbox** — added to all three form instances with standard consent
  copy, since the doc flagged phone numbers being collected with no consent language. **Legal/
  compliance should review and approve the exact wording** before launch.
- **Hidden fields** on every form: `form_source` (hero/bottom/popup), `page_url`, `timestamp`,
  and `utm_source/medium/campaign/content/term` (read from the query string) — ready for
  whenever the CRM integration is built.
- **Inline success state** — since there's no CRM endpoint or thank-you URL yet, a valid
  submit swaps the form for an on-page confirmation message instead of redirecting.
- Footer phone number is a working `tel:` link. Phone inputs auto-format as you type.
- Added the "Solution Bridge" section (eyebrow "One Number. One Inbox.") — it was present in
  the copy doc (Section 5, #7) but missing from the original HTML draft.

## ⚠️ Still needs input before this goes live

These were left blank in the build doc and need the client/build owner's answer:

1. **Domain / subdomain** this deploys to, and the Cloudflare Pages project/worker name.
2. **CRM endpoint URL, auth, and payload format** — `sendLeadToCRM()` in `interactions.js`
   is a no-op stub marked with a `TODO` until this exists.
3. **GTM / GA4 / CallRail / Search Console IDs** — placeholders with the correct placement
   are already commented into `BaseLayout.astro`; drop the real IDs/snippets in and
   uncomment.
4. **Thank-you page URL**, if a redirect is preferred over the inline success message.
5. **Exact TCPA consent copy** — see above.
6. **Privacy Policy / Terms of Service / Sitemap** URLs (footer links are `#` placeholders).

## A note on the Meta Conversions API token

The build doc included a Meta Conversions API **access token**. That's a server-side secret,
not a snippet — it must never ship in client/static code (this project is 100% static, so
anything in this repo is publicly visible in the browser). It has **not** been placed
anywhere in this project. When the CRM/CAPI integration is built, store it as an encrypted
Cloudflare Pages environment variable and call the Conversions API from a server-side
function (a Cloudflare Pages Function), never from the browser.
