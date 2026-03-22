# Volume Flow Anomaly · AnomIQ Preview

A TradingView Pine Script indicator that approximates AnomIQ's real-time microstructure scanner. Detects anomalous volume events using rolling z-scores, trend cleanliness, and relative price impact — the same metrics the [AnomIQ platform](https://anomiq.io/?utm_source=github&utm_medium=readme&utm_campaign=vfa_indicator) computes from tick-level data.

> **This is a preview.** The buy/sell split uses bar-range estimation, not tick-level taker classification. The full platform uses real taker data, cross-market scanning across all Binance and Coinbase pairs, and real-time alerts via Telegram and NTFY.

---

## How It Works

Every signal in this indicator is relative — calculated against a coin's own rolling volume history, not a fixed threshold. A 2σ event on BTC and a 2σ event on a small-cap altcoin represent the same statistical rarity for that coin. This is what makes the signals usable across any market cap.

### Three core metrics

**Vol Z** — rolling z-score of aggregated window volume against the lookback period. Tells you whether current volume is statistically significant relative to this coin's own history.

**Trend Cleanliness** — directional consistency of price movement within the window. High cleanliness means bars are stacking in one direction. Low cleanliness with high volume = absorption.

**Relative Price Impact (RPI)** — volume per unit of price movement. High volume with low RPI means a large passive order is absorbing flow. The move that follows is often directional.

---

## Signal Lifecycle

Signals follow a three-phase structure. Trading the phases, not the individual signals, is the intended use.

```
Yellow dot  →  Absorption diamond  →  Triangle
Setup       →  Entry               →  Exit warning
```

### Phase 1 — Setup
**🟡 Yellow dot** — Vol Z is building (above early threshold) but price has no clear direction yet (Trend Cleanliness < 35%). The fuel is loading. This fires 1–3 bars before the main signal. Watch, do not enter yet.

### Phase 2 — Entry
**🔶 Absorption diamond** — High volume (Vol Z ≥ threshold), minimal price movement (window return < 0.3%), low RPI. A large passive order is absorbing aggressive flow. The directional move typically follows within 2–5 bars.

- **Teal diamond** — buy side absorbing (bullish lean)
- **Red diamond** — sell side absorbing (bearish lean)

This is the most actionable signal. It is leading, not lagging.

### Phase 3 — Exit Warning
**🟢 Teal triangle** — Upside move confirmed. Vol Z ≥ threshold, Trend Cleanliness ≥ minimum, positive window return. The move already happened with real volume behind it. Trail your stop or watch for a pullback entry.

**🔴 Red triangle** — Same logic, inverted.

Triangles are lagging. They confirm a move had volume behind it. Do not enter on a triangle — the fuel is already consumed.

### Extreme Volume
**🟠 Orange dot** — Vol Z ≥ 3.5σ with no confirmed direction or absorption pattern yet. Exceptional activity. Classify manually.

---

## Settings

| Parameter | Default | Description |
|---|---|---|
| Window Length | 5 | Number of bars to aggregate. Use 5 on 1m to match AnomIQ's 5m timeframe. |
| Historical Lookback | 100 | Bars used for z-score baseline. Higher = slower to adapt, more stable. |
| Vol Z Threshold | 2.0 | Minimum z-score to trigger a confirmed signal. |
| Early Warning Vol Z | 1.3 | Z-score that triggers the setup dot. Lower = earlier but noisier. |
| Trend Cleanliness Min% | 50 | Minimum directional consistency required for surge signals. |
| Min Window Return % | 0.3 | Minimum price move required to classify as a surge (not absorption). |

**Recommended starting configuration:** 1m chart, Window=5, Lookback=100. This approximates AnomIQ's 5m timeframe scanner.

---

## Alerts

Four alert conditions are available under `Add Alert → Condition`:

| Alert | Fires when |
|---|---|
| AnomIQ · Early Warning | Volume building without committed direction |
| AnomIQ · Upside Surge Confirmed | Upside move confirmed with anomalous volume |
| AnomIQ · Downside Surge Confirmed | Downside move confirmed with anomalous volume |
| AnomIQ · Absorption | High volume absorbed without price impact |

---

## Limitations

**Buy/sell split is approximated.** This script estimates buy and sell volume using bar-range geometry: `buy_fraction = (close - low) / (high - low)`. This is a common approximation but is less accurate than tick-level taker classification, particularly on wide-spread candles or during gaps.

The AnomIQ platform uses real taker data from exchange WebSocket feeds. The Vol Z line and Trend Cleanliness metric are fully accurate in both this script and the platform.

**Single-pair only.** This indicator runs on one chart at a time. AnomIQ scans the entire Binance and Coinbase universe simultaneously, surfacing anomalies across all pairs in real time.

---

## Full Platform

[AnomIQ](https://anomiq.io/?utm_source=github&utm_medium=readme&utm_campaign=vfa_indicator) is the real-time version of what this indicator approximates:

- Scans all Binance and Coinbase pairs simultaneously
- Tick-level taker classification (not bar-range estimation)
- Custom scanner builder with z-score, buy/sell ratio, volume, and taker imbalance filters across 5m / 15m / 60m timeframes
- Real-time alerts via web dashboard, Telegram, and NTFY
- Signals relative to each coin's own history — the same filter works for BTC and a small-cap altcoin

Free tier available. Real-time results and Telegram/NTFY alerts on paid tier.

---

## Installation

1. Open TradingView and go to the Pine Script editor (bottom panel)
2. Paste the contents of `volume-flow-anomaly.pine`
3. Click **Add to chart**
4. Set chart timeframe to **1m** for default settings

Or search for **"Volume Flow Anomaly AnomIQ"** in TradingView's public indicator library.

---

## License

MIT. Use freely, modify freely. Attribution appreciated but not required.
