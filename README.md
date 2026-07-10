# Vantage

A single-file stock tracker with a composite technical score. One HTML file, no build step, no server, no dependencies. Open it in a browser and it works.

![status](https://img.shields.io/badge/build-single%20file-e4b75c) ![licence](https://img.shields.io/badge/data-Twelve%20Data-3fbf8a)

## What it does

- Ranks a 32-stock universe (plus anything you search for) by a transparent composite score built from standard technical indicators
- Detail view per stock: price chart with Bollinger bands and 20/50 day moving averages, RSI, MACD, a stat grid, and a plain-English reading of what the numbers say
- Profiles with PIN unlock and per-profile favourites, useful on a shared device
- Filters for the setups screeners care about: buy watch, sell watch, oversold, overbought
- Two data modes: a simulated market that works offline, and live daily bars from Twelve Data

## Quick start

1. Download `vantage.html` (or clone this repo)
2. Open it in any modern browser
3. Create a profile with a name and a 4 to 8 digit PIN

That is the whole install. The app starts in demo mode with a simulated market, clearly labelled, so you can explore every feature without a key.

## Going live

1. Get a free API key at [twelvedata.com](https://twelvedata.com)
2. In the app, open Settings, paste the key, and press Save and test
3. The pill in the top bar flips from Demo to Live

The free tier allows 8 API calls per minute and 800 per day. Vantage throttles itself to stay under the per-minute cap and caches each stock for 15 minutes, so normal browsing is cheap. Loading the full universe costs about 32 credits and takes a few minutes, which is why live mode starts with the indices and your favourites and offers the full load as an explicit button.

## Hosting on GitHub Pages

Rename the file to `index.html`, push it to a repo, then enable Pages in the repo settings (deploy from branch, root folder). The hosted file is safe to make public: your API key is never in the code. It is entered in the UI and stored only in that browser's localStorage, so visitors get demo mode until they bring their own key.

## How the score works

Each stock gets a composite in the range minus 100 to plus 100, built from three pillars. Every component is clamped to a minus one to plus one range before averaging.

| Pillar | Weight | Components |
| --- | --- | --- |
| Trend | 0.40 | price vs SMA50, SMA20 vs SMA50, MACD line |
| Momentum | 0.35 | RSI centred at 50, MACD histogram, 14 day rate of change |
| Stretch | 0.25 | z-score vs 50 day mean, Bollinger %B, RSI extremes |

The weights are defaults, not discovered truths. Momentum and mean reversion pull in opposite directions and no fixed blend wins in all market regimes. The indicator maths is unit tested in Node and cross-checked against an independent Python implementation.

## Honest limitations

- Everything is computed from past daily prices over about six months. The natural horizon is days to a few months, not long-term investing. There are no fundamentals here.
- The score describes where a chart sits statistically. It does not forecast where it goes next, and nothing simple does.
- The PIN separates profiles on a shared device. It is not real security, since browser storage is readable by anyone with devtools access.
- Profiles, favourites and the API key live in localStorage, so they do not sync between devices.

Vantage summarises past price behaviour with standard technical statistics. Signals are descriptive, not predictive, and nothing here is financial advice.

## Tech notes

Vanilla JavaScript, hand-drawn canvas charts, no frameworks and no build tooling. Data by [Twelve Data](https://twelvedata.com) when live, or a seeded geometric Brownian motion simulator when offline. PIN hashing uses SHA-256 via `crypto.subtle`, which needs a secure context, so serve over https or open the file locally.
