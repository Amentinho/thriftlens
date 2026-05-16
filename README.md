# 🏷️ ThriftLens

Real-time thrift price scanner. Point your phone camera at any item at a flea market — get instant resale price estimates from 8 European second-hand platforms and a buy/pass recommendation.

## Features

- 📷 Live camera scanning (no photo needed — auto-scans every N seconds)
- 🤖 Claude Vision AI identifies brand, category, condition, size
- 💰 Prices aggregated from 8 EU platforms in parallel:
  - eBay EU, Vinted, Depop, Wallapop, Vestiaire Collective, StockX, LeBonCoin, Subito.it
- 📊 Weighted resale price + P25/P75 range per platform
- 🔥 Sell propensity score (0–100%)
- 💶 Buy calculator: type the seller's price → instant BUY / MAYBE / PASS
- 🗂 Session history with thumbnails

## Setup

### 1. Enable GitHub Pages
Go to repo **Settings → Pages → Source: main branch → / (root)** → Save.

Your app will be live at: `https://amentinho.github.io/thriftlens`

### 2. Get your API keys

**Anthropic API key** (required for vision):
- Go to [console.anthropic.com](https://console.anthropic.com)
- Create API key
- ~$0.003 per scan

**Apify token** (optional — for real scraped prices):
- Go to [apify.com](https://apify.com) → free tier available
- Without it, prices are estimated from market models (still useful)

### 3. Open on iPhone Safari

1. Go to `https://amentinho.github.io/thriftlens` in Safari
2. Tap Share → **Add to Home Screen** (makes it a PWA)
3. Open from home screen → allow camera
4. Go to ⚙️ Settings → add your Anthropic API key
5. Tap 🚀 Start Scanning

## Usage

1. At the market, open ThriftLens
2. Point camera at any clothing item or object
3. Hold steady for 2–3 seconds
4. See: brand identification + price range + sell propensity
5. Type the seller's asking price → get BUY / MAYBE / PASS signal
6. Margin shown: `+X%` = how much you'd profit reselling on Vinted

## Architecture

```
Camera stream → Claude Vision API
              → 8 platform scrapers (parallel)
              → Price aggregation + propensity score
              → Buy/sell recommendation
```

## Cost estimate per market visit

| | Cost |
|---|---|
| Claude Vision (~50 scans) | ~€0.15 |
| Apify scraping (~50 searches × 8 platforms) | ~€0.40 |
| **Total** | **~€0.55/visit** |

## Tech stack

- Vanilla React (no build tool — runs directly in Safari via Babel CDN)
- Claude claude-sonnet-4-20250514 Vision
- Apify actors for platform scraping
- PWA (installable, offline-capable)
