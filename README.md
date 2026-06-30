# Telekom.de Heatmap Demo (Amplitude)

A static, single-page replica of the structure and content of [telekom.de/start](https://www.telekom.de/start), built **specifically to let Deutsche Telekom self-test Amplitude Heatmaps** in their own Amplitude project.

> **Why a static clone instead of a Lovable-built demo with synthetic data?**
> Heatmaps are powered by **real browser-side autocapture** (click coordinates, element selectors, viewport size) — not by historical events generated via the HTTP API. Synthetic backend events (the kind the `repeatable-platform-demo-generator` skill normally produces for funnel/Conversion-Drivers demos) carry no DOM/coordinate data and are invisible to Heatmaps. So this site intentionally ships with **no synthetic data pipeline** — Telekom generates their own heatmap data live, just by clicking around.

---

## What's instrumented

- **Amplitude Browser SDK 2** (`@amplitude/analytics-browser`, loaded via CDN)
- **Autocapture with `elementInteractions: true`** — this is the flag that actually populates Heatmaps (clicks, rage clicks, dead clicks on real DOM elements)
- **Session Replay plugin** (`sampleRate: 1` — 100% capture, since this is a controlled test, not production traffic)
- **Page views, sessions, form interactions, file downloads** autocapture also enabled
- **EU data residency** (`serverZone: "EU"`) — pointed at Amplitude EU

Project details used in this build:

| Setting | Value |
|---|---|
| Project ID | `100051718` |
| Data Center | EU |
| API Key (Browser/SDK key, not HTTP API key) | `3495b924a5f3ca762061d61cbe21013d` |

⚠️ **This is a Browser/SDK key, safe to ship client-side** — it is not the HTTP API key, which must never be exposed in a public file like this.

---

## How to deploy on GitHub Pages

1. Create (or reuse) a public repo, e.g. `telekom-heatmap-demo`.
2. Add `index.html` (this file) to the repo root.
3. In repo **Settings → Pages**, set source to `main` branch, `/ (root)`.
4. GitHub will publish the site at:
   `https://<your-github-username>.github.io/telekom-heatmap-demo/`
5. Share that URL with your Telekom contacts.

---

## How Telekom should test Heatmaps

1. Open the deployed GitHub Pages URL.
2. Click around naturally — nav links, the WM 2026 hero CTA, product cards in all three tabs (Internet & Glasfaser / Mobilfunk / Für unsere Kunden), the "Ich möchte…" quick links, footer links, app store badges.
3. Switch between the three category tabs a few times (`showCategory()` swaps visible sections and fires a `Category Tab Viewed` event) — Heatmaps work per-URL/per-view, so repeated interaction on the same view builds up a denser map.
4. In Amplitude, go to **Heatmaps** (or **Session Replay → Heatmaps** depending on your org's nav) and select this page's URL. Click density should appear after a handful of sessions.
5. To also show **Session Replay** alongside the Heatmap, open **Session Replay**, find a recent session from this site, and play it back — replay and heatmap data come from the same instrumentation in this build.

> Tip for the live demo: have 2–3 people click through the page from different devices/browsers first (mobile + desktop ideally), so the Heatmap and replay list aren't empty when you walk Telekom through it live.

---

## Notes / things to double check before the customer touches it

- This is a **visual and structural replica**, not a pixel-perfect or legally-licensed copy of telekom.de — product copy has been shortened/paraphrased and most images are placeholders or hot-linked from Telekom's own public CDN for layout purposes only. Treat it as a demo prop, not a published page.
- All links are dead (`href="#"` or tab-switch JS) by design — this avoids accidentally sending real Telekom customers into the live site mid-demo. Every clickable element fires either a `data-nav` autocapture interaction or, for product cards, an explicit `track()` call — both feed Heatmaps and standard analytics.
- 100% Session Replay sample rate is intentional for a low-traffic test project. **Turn this down (e.g. 0.1–0.3) if this ever gets meaningful real-user traffic**, to stay within replay quota.
- If Heatmap data doesn't appear after testing: confirm `elementInteractions` autocapture is actually enabled at the **project level** in Amplitude settings too (some orgs gate it behind a project toggle in addition to the SDK config).
