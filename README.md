# StockenShares

A global paper trading simulator. Every user gets $10,000 virtual cash to trade real, live-priced assets across world markets. No brokerage account, no email verification, no payment info.

**Live data. Zero cost. 35+ assets across 8 exchanges.**

**Live app: https://stockenshares.onrender.com/** 

---

## Why

Most paper trading apps fail on one of three things:
- Paywalled or require a real brokerage account
- Only support US stocks
- Use fake/simulated prices instead of real market data

StockenShares fixes all three. Trade `RELIANCE.NS` (NSE), `AAPL` (NASDAQ), `BTC-USD`, Gold futures, `SAP.DE`, Toyota (Tokyo), and more, all at real prices, the moment you sign up.

## Features

- **Global coverage**: NSE, BSE, NASDAQ, NYSE, LSE, Frankfurt, Tokyo, HK, ASX, TSX
- **Natural language search**: type "reliance" or "crude oil", Groq LLaMA 3.3 resolves the ticker (falls back to suffix matching if AI is down)
- **Multi-currency**: 17 live exchange rates, all internal math in USD
- **Dual chart engine**: TradingView widget for US stocks, custom Lightweight Charts (with MA20/50/200 overlays) for everything else
- **Live leaderboard**: ranked by real-time portfolio return
- **6 themes**: Raspberry, Matrix (with canvas rain animation), Terminal, Light, Pink, Red
- **Weighted avg cost basis**: real brokerage-style gain/loss tracking
- **Parallel ticker tape**: 14 live prices refreshed every 90s via `ThreadPoolExecutor`

## Stack

| Layer | Tech |
|---|---|
| Backend | Flask (single `app.py`) |
| Frontend | Vanilla JS, single `index.html`, no framework |
| Database | MongoDB Atlas (M0 free tier) |
| Market data | yfinance + Finnhub |
| AI ticker resolution | Groq (LLaMA 3.3 70B) |
| Hosting | Render (free tier) |

No build tools. Clone it, `python app.py`, done.

## Setup

```bash
git clone <repo-url>
cd stockenshares
pip install -r requirements.txt
```

Create a `.env` (or set these in your host's dashboard):

```
GROQ_API_KEY=
FINNHUB_API_KEY=
MONGO_URI=
SECRET_KEY=
```

```bash
python app.py
```

## How a trade works

1. User searches "reliance", Groq resolves it to `RELIANCE.NS`
2. Server fetches live price and INR/USD rate from Yahoo Finance
3. User confirms buy, server re-fetches rate, deducts USD-equivalent cash, saves holding and trade history to MongoDB
4. Portfolio/leaderboard re-fetch live prices on every load, so numbers are always current

## Known limitations

- Passwords stored in plaintext (fine for a demo, not production)
- Leaderboard re-fetches live prices per user per load, won't scale past a few hundred users without a caching layer
- Render free tier cold-starts (20 to 40s) after 15 min idle
- TradingView charts only work for US tickers (format mismatch with Yahoo's `.NS`/`.BO`/`^` symbols, solved via a separate Lightweight Charts pipeline for everything non-US)
- Flask sessions are cookie-based, won't survive multi-instance load balancing without Redis
