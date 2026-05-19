# 🏷️ ThriftLens

Real-time thrift price scanner for flea markets and second-hand shops.  
Point your phone at any item → get instant resale prices from Vinted and eBay → decide BUY or PASS in seconds.

**Live at:** [amentinho.github.io/thriftlens](https://amentinho.github.io/thriftlens)

---

## How it works

1. Open ThriftLens in Safari on your iPhone
2. Point camera at any item (clothing, cameras, watches, books, vinyl, antiques...)
3. Tap 📸 or wait for auto-scan
4. See: item identification + estimated price instantly
5. Real Vinted prices load in ~10–30s in the background
6. Type the seller's price → get **BUY / MAYBE / PASS** signal with margin %
7. Tap **✅ BUY** or **❌ PASS** → saved to history → camera restarts

---

## Architecture

```
iPhone Safari
    │
    ├── GPT-4o Vision (identify item)
    │       └── via Cloudflare Worker /vision
    │
    ├── Vinted Smart Scraper (real listing prices)
    │       └── via Cloudflare Worker /apify → Apify API
    │
    └── eBay (real listing prices)
            ├── Official Browse API (when EBAY_APP_ID set)
            └── HTML scrape fallback (always available)

All API keys stored in Cloudflare Worker — never exposed in browser.
```

---

## Data sources

| Platform | Status | Data |
|---|---|---|
| **Vinted** | ✅ Live | Real scraped listings via Apify (kazkn/vinted-smart-scraper) |
| **eBay EU** | ✅ Live (scrape) / ⭐ Official API (when approved) | Active EU listings in EUR |

Prices start as estimates (~0ms) then update with real data as it arrives.  
Each platform shows **`live`** or **`est`** badge so you always know which is real.

---

## Features

- 📷 Live camera — auto-scans every N seconds or manual tap
- 🤖 GPT-4o Vision — identifies brand, model, condition, category
- 💰 Instant estimated prices → real prices load progressively
- 📊 Sell propensity score (0–100%) based on demand/supply signals
- 💶 Buy calculator — type seller's price → BUY/MAYBE/PASS + margin %
- 📲 WhatsApp sharing — send scan results to friends
- 🗂 Persistent scan history (IndexedDB) — survives page reloads
- ✓/✗ Decision tracking — know what you bought vs passed
- 📊 Stats: total scanned, bought, passed, top category
- 🔍 Clarify flow — if item not recognised, tap brand from list

---

## Categories supported

Clothing · Shoes · Bags · Cameras · Watches · Books · Vinyl records  
Electronics · Antiques · Toys · Jewelry · Accessories

---

## Setup

### 1. Cloudflare Worker (API proxy — keeps keys secure)

1. Go to [workers.cloudflare.com](https://workers.cloudflare.com) → Create Worker
2. Click **Start with Hello World** → paste `worker.js` → Deploy
3. Go to **Settings → Variables and Secrets** → add:
   - `OPENAI_KEY` = your OpenAI API key (from [platform.openai.com](https://platform.openai.com))
   - `APIFY_TOKEN` = your Apify token (from [apify.com](https://apify.com) → Settings → Integrations)
   - `EBAY_APP_ID` = eBay App ID *(optional — when developer account approved)*
   - `EBAY_CERT_ID` = eBay Cert ID *(optional — pair with EBAY_APP_ID)*
4. Note your worker URL: `https://thriftlens.YOUR-SUBDOMAIN.workers.dev`

### 2. GitHub Pages

1. Fork or clone this repo
2. In `index.html` confirm `WORKER_URL` matches your worker URL (already set to Andrea's worker)
3. Go to repo **Settings → Pages → Source: main branch → / (root)** → Save
4. Site live at `https://YOUR-USERNAME.github.io/thriftlens`

### 3. Use on iPhone

1. Open the GitHub Pages URL in **Safari**
2. Tap Share → **Add to Home Screen** → opens as a PWA app
3. Allow camera when prompted
4. Go to ⚙️ to set scan interval and auto-scan preference

---

## Cost estimate per market visit (~50 scans)

| Service | Cost |
|---|---|
| OpenAI GPT-4o vision | ~€0.15 |
| Vinted via Apify | ~€0.09 (50 searches × $0.0018/run) |
| eBay scrape | Free |
| Cloudflare Worker | Free (100k req/day) |
| **Total** | **~€0.25/visit** |

---

## Adding eBay official API

When your eBay developer account is approved ([developer.ebay.com](https://developer.ebay.com)):

1. Create an app → get **App ID (Client ID)** and **Cert ID (Client Secret)**
2. Add both as secrets in your Cloudflare Worker
3. Worker automatically switches from HTML scrape to official API
4. Benefits: more reliable, returns condition-filtered listings, EUR prices

---

## File structure

```
thriftlens/
├── index.html      # Full PWA app (HTML + CSS + JS, no build needed)
├── worker.js       # Cloudflare Worker — API proxy, keeps keys secure
├── manifest.json   # PWA manifest
└── README.md       # This file
```

---

## Roadmap

- [ ] eBay official API (when account approved)
- [ ] RAG price injection — use past scan history to improve AI estimates
- [ ] AI glasses support (Mentra Live SDK)
- [ ] Barcode scanning for books/vinyl (ISBN → instant price)
- [ ] Price alerts — notify when similar item drops below threshold
