# Renewal Africa Foundation — Microsite

A single-file, no-build website for Renewal Africa Foundation, a Kenya-registered Public
Benefit Organization working across four programme areas: WASH & Health, Environment &
Clean Energy, Sustainable Agriculture & Livelihoods, and Protection & Social Inclusion.

Everything — markup, styling, and behaviour — lives in one file: **`index.html`**. There
is no build step, no package manager, and no server-side code.

## Features

- Hash-based multi-page routing (Home, About, Board, four theme pages, Dashboard, Field
  Map, Voices, Support & Contact) inside a single HTML file
- A client-side, password-gated **admin mode** for editing content directly on the live
  site — add/remove map pins and field-voice stories, change the admin password
- A live **field map** (Leaflet) with geocoded pin placement via OpenStreetMap Nominatim
- An **activities dashboard** (Chart.js) with a monthly trend line and a programme-reach
  doughnut chart
- A donation funnel, contact form, and FAQ accordion
- Scroll-reveal animations, animated counters, header shrink-on-scroll, and a back-to-top
  button — all respecting `prefers-reduced-motion`
- Fully responsive, including a mobile slide-out nav menu

## Running it locally

Because the admin login uses the Web Crypto API (`crypto.subtle`), which browsers only
expose in a **secure context**, don't just double-click `index.html` and open it via
`file://` — `crypto.subtle` is unavailable there in most browsers, so admin login will show
a "needs a secure connection" message. Instead, serve the folder locally:

```bash
# Python (built into most systems)
python3 -m http.server 8000

# or Node
npx serve .
```

Then open `http://localhost:8000`. `localhost` counts as a secure context, so everything
— including admin login — works exactly as it will once deployed.

## Deploying to GitHub Pages

1. Push this repo to GitHub.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick your default
   branch, and set the folder to `/ (root)`.
4. Save. GitHub will publish `index.html` at `https://<your-username>.github.io/<repo>/`
   within a minute or two — no build step required, since `index.html` is already at the
   repo root.

GitHub Pages serves over HTTPS by default, so admin login and all `crypto.subtle`-based
features work there without any extra configuration.

## About the "admin mode" and data persistence

This site was originally built to run inside Claude.ai, where a special `window.storage`
API lets admin edits (map pins, stories, the admin password) persist **for every visitor**,
hosted by Claude. That API doesn't exist outside Claude.ai.

To keep the site fully testable once it's hosted elsewhere (GitHub Pages, or anywhere
else), `index.html` includes a small shim near the top of its `<script>` block: if
`window.storage` isn't present, it transparently swaps in a `localStorage`-backed
implementation with the same interface. Practically, that means:

- **Inside Claude.ai's artifact preview:** admin edits are shared across every visitor.
- **Everywhere else (GitHub Pages, local server, etc.):** admin edits persist across
  reloads, but **only in that one browser** — `localStorage` doesn't sync across devices
  or visitors. This is expected and fine for demoing/testing the admin flow; it is not a
  substitute for a real backend if you need edits to be visible to other people.

**Default admin password:** `renewal2026` — change it via the "Change password" button in
the admin bar after logging in once. Do this before sharing a public link if you don't want
other people editing the live content.

## Known placeholders

A few things are intentionally marked as placeholders rather than quietly faked as real,
and are flagged as such in the UI itself:

- The CEO card and three non-executive director cards use placeholder names (flagged with
  a "Sample name" badge) pending real appointments/confirmation.
- The field map, activity ticker, and dashboard use representative sample data until
  connected to a real reporting feed.
- The donation funnel doesn't process real payments — wire it up to a real processor
  (Stripe, PayPal, etc.) before going live with real fundraising.
- The contact form shows a confirmation message but doesn't send email — connect it to a
  real mail service or CRM before relying on it.

Five of the board members (Sylvester, Judy, Noel, David, Sepjune) have real photos already
embedded.

## Tech / dependencies

Everything loads from CDNs at runtime (no bundling):

- [Leaflet 1.9.4](https://leafletjs.com/) — field map
- [Chart.js 4.4.1](https://www.chartjs.org/) — dashboard charts
- [Google Fonts](https://fonts.google.com/) — Space Grotesk, Source Serif 4, IBM Plex Mono
- [OpenStreetMap Nominatim](https://nominatim.org/) — geocoding for admin-added map pins

This means the site needs an internet connection to look/work fully correctly, even when
served locally — the fonts, map tiles, and chart/map libraries themselves are all fetched
from the CDNs above, not bundled into `index.html`.

## Testing checklist

- [ ] Site loads with no console errors (open DevTools → Console)
- [ ] All top-nav links switch pages correctly; browser back/forward works
- [ ] Mobile menu (resize under ~980px, or use device emulation) opens and every link works
- [ ] Admin login works with the default password; "Change password" works
- [ ] With admin mode on: add a map pin (try a real place name), refresh, confirm it's
      still there; delete it
- [ ] With admin mode on: add a story/testimonial with a photo, refresh, confirm it
      persisted; delete it
- [ ] Dashboard charts render (needs Chart.js CDN to load — check your network if blank)
- [ ] Field map renders with pins and a legend (needs Leaflet CDN + internet for tiles)
- [ ] Donate page: frequency toggle, amount selection, and the impact-hint text all update
- [ ] Contact form submits (shows a confirmation) and FAQ accordion expands/collapses
- [ ] Scroll down any page — reveal animations and the back-to-top button should appear

## License / content ownership

This repo contains no explicit license file. The code structure (routing, admin-editing
pattern, etc.) is simple enough to reuse freely, but the actual site *content* — copy,
board member names and photos, the organization's logo — belongs to Renewal Africa
Foundation and shouldn't be treated as open-source-licensed by default. Add a `LICENSE`
file yourself if and once you've decided what's appropriate (e.g. code under MIT, content
under all-rights-reserved).
