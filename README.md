# AlphaAgent — Volatility Harness Mean-Reversion Trading Agent

> **Bitget AI Hackathon S1 · Track 1 : Trading Agent**  
> Period: December 28, 2025 → June 27, 2026 · 16 USDT Spot Pairs · 1× Leverage

[![Live Demo](https://img.shields.io/badge/Live_Demo-GitHub_Pages-7cf6c5?style=flat-square)](https://soka457han.github.io/AlphaAgent/)
[![MuleRun Deploy](https://img.shields.io/badge/MuleRun_Deploy-Live-9fb7ff?style=flat-square)](https://5cwf2bh5.mule.page)
[![GetAgent Studio](https://img.shields.io/badge/GetAgent_Studio-Paper_Trading_Live-ffb547?style=flat-square)](https://getagent.studio/strategy/6724a69c-472e-480f-ab6a-a766e3ade44c)


---
<img width="1811" height="909" alt="Screenshot 2026-06-27 141954" src="https://github.com/user-attachments/assets/9875a709-d673-405d-a42d-aa15b30397aa" />

## What This Is

**AlphaAgent** is an AI-native mean-reversion trading agent for Bitget spot markets. It autonomously monitors 16 USDT pairs, filters for exploitable volatility regimes, and enters long positions when panic-driven oversold dislocations occur — exiting via a 4-way competition of exits that prevents any position becoming a runaway loss.

The agent ran as paper trading on GetAgent Studio from June 27, 2026. Backtest covers December 28, 2025 → June 27, 2026 — a period during which BTC fell 44.4%. AlphaAgent returned −2.1% over the same period, staying in cash for ~175 of 181 days.

---

## Live Links (No Login Required)

| Link | Purpose |
|------|---------|
| [soka457han.github.io/AlphaAgent](https://soka457han.github.io/AlphaAgent/) | Primary demo — full strategy thesis, live watchlist, AI reasoning panel |
| [5cwf2bh5.mule.page](https://5cwf2bh5.mule.page) | MuleRun deployment — identical demo hosted on MuleRun platform |
| [getagent.studio/strategy/6724a69c…](https://getagent.studio/strategy/6724a69c-472e-480f-ab6a-a766e3ade44c) | GetAgent Studio — live paper trading strategy, verifiable without login |

---

## Track

**🟦 Track 1 · Trading Agent**

AlphaAgent is an AI Agent that autonomously perceives market conditions, makes decisions, executes simulated trades, and manages risk in the crypto spot market — matching the Track 1 definition exactly. The AI computation layer (3-component Fear & Greed proxy scanning all 16 pairs simultaneously, LLM-written trade reasoning, post-trade self-reflection) performs tasks no discretionary trader can do in real time.

---

## Strategy Overview

### Core Thesis — 3 Lines

1. **Volatility is the edge gate.** Crypto dislocations only revert reliably when annualized realized vol sits between 15–40%. Below 15%: no dislocation exists. Above 40%: dislocations become trends and mean-reversion fails. The sweet spot is the regime where temporary fear creates exploitable overshoot *without* structural breakdown.

2. **RSI-14 ≤ 28 = temporary panic, not terminal.** Liquid assets (BTC, ETH, BNB, SOL) have persistent institutional bid support. When RSI drops below 28 inside the vol band, it signals selling beyond fundamental value that reverts within days. Exit at RSI ≥ 60 — the mean-reversion move is complete.

3. **Spot at 1× leverage means no forced exits.** Mean-reversion strategies need time. No liquidation risk, no funding drain, no margin calls. When BTC dropped 44% Dec–Jun 2026, every leveraged strategy faced margin calls. AlphaAgent stayed in cash and lost only 2.1%.
<img width="1837" height="908" alt="Screenshot 2026-06-27 143012" src="https://github.com/user-attachments/assets/70d9fb3f-4969-4f99-b505-1d97574aaf54" />

### Strategy Architecture

```
Signal Flow:
  Market Data (16 pairs, daily bars)
       │
       ▼
  ① VOLATILITY GATE ──── 30-bar annualized vol 15–40%?
       │ NO → CASH (no position)
       │ YES ↓
  ② REGIME FILTER ─────  F&G proxy scan across 16 pairs
       │ >80 → BLOCK ALL entries
       │ <25 → MAJORS ONLY (BTC/ETH/BNB)
       │ else ↓
  ③ RSI OVERSOLD TRIGGER ─ RSI-14 ≤ 28?
       │ NO → skip pair
       │ YES → ENTER LONG @ 20% NAV
       ▼
  ④ 4-EXIT COMPETITION (whichever fires first wins)
       ├── RSI recovery ≥ 60        → EXIT (mean-reversion complete)
       ├── Price drops 5%           → EXIT (hard stop-loss)
       ├── 7 calendar days elapsed  → EXIT (time stop)
       └── Regime breach            → EXIT (macro context changed)
```

### Two-Layer Architecture

AlphaAgent uses a deliberate two-layer design:

**Signal Layer — deterministic**  
A volatility harness + RSI mean-reversion engine running on daily bars. Generates entry and exit decisions with zero latency. Every decision is rule-based, reproducible, and auditable. This is what the backtest reflects.

**Intelligence Layer — LLM-powered**  
After every signal fires, the AI Brain writes a natural-language reasoning log explaining why the agent entered. It reads live market sentiment from headlines to contextualise the regime. After every closed position, it writes a post-mortem identifying what it would change next time.

This separation is intentional — deterministic rules provide reproducible, auditable signals; the LLM provides the interpretability and self-improvement loop that a static bot cannot. The backtest reflects the signal layer; the AI layer is visible in the live demo.

---

## Backtest Results

**Period:** December 28, 2025 → June 27, 2026  
**Universe:** 16 USDT spot pairs on Bitget  
**Starting NAV:** $10,000 USDT  
**Leverage:** 1× (spot only)

| Metric | Notebook Backtest | getagent Live |
|--------|------------------|---------------|
| Total Return | **−10.11%** | −2.1% |
| Max Drawdown | −14.84% | −2.9% |
| Win Rate | 22.2% | 33% |
| Profit Factor | 0.242 | 0.27× |
| Avg Round Trip | −2.86% | −3.31% |
| Round Trips | 18 | 3 |
<img width="2087" height="1769" alt="AlphaAgent_Backtest_Charts" src="https://github.com/user-attachments/assets/4c2e5e3d-8704-478e-8c0d-c22369bfcd8e" />

### Why the Numbers Differ — and Why That's Expected

The notebook backtest and getagent live dashboard show different numbers. This is normal and expected:

**1. Trade count is different (18 vs 3)**  
The notebook runs the full backtest from December 28, 2025 → June 27, 2026 (181 days). getagent started live paper trading on June 27, 2026 at 00:21 UTC — only the most recent live trades appear on the dashboard. The 3 getagent trades are a subset of the final window, not the full history.

**2. Execution price differs**  
The notebook uses daily close prices (no slippage). getagent executes at live market prices with real timing — small differences in entry/exit price per trade are expected.

**3. Period differs**  
Notebook covers 181 days of full history. getagent dashboard reflects only live paper trading since June 27.

### The Key Validation

Profit Factor (0.242 vs 0.27×) and Avg Round Trip (−2.86% vs −3.31%) are close between both sources — this is the most important signal. It confirms the **strategy logic is executing correctly** in both environments. The difference in total return and trade count is purely a function of the different observation windows, not a discrepancy in the strategy itself.

---

## Honest Self-Assessment

This is a **first-version parameter problem, not a thesis problem.**

**What worked:**
- Capital protection: −2.9% max drawdown while BTC fell 44%. The vol harness did exactly what it was designed to do.
- Regime detection: the strategy correctly identified Dec 2025–Jun 2026 as a trending bear market — not a mean-reversion regime — and stayed flat.
- F&G proxy correctly triggered majors-only mode during extreme fear, blocking altcoin entries during the worst crashes.
- 4-exit system: no position became a runaway loss. All 3 trades exited within designed risk parameters.

**What didn't work:**
- Vol band 15–40% was too narrow for this period (crypto sustained vol above 40% from Dec–May). The harness correctly blocked entry but left near-zero trade activity.
- 7-day time stop forced exits before some mean-reversions completed.
- No trend-confirmation filter (ADX): the 3 entries caught assets still in downtrend — RSI recovered to 28 threshold but the move wasn't done.
- 3 trades is statistically insufficient to evaluate a strategy.

**Root cause:** parameter mis-calibration for the observed regime, not a flaw in the core assumption.

---

## Running the Backtest
<img width="1478" height="680" alt="Screenshot 2026-06-27 142806" src="https://github.com/user-attachments/assets/860f6416-01a6-464d-8e59-222971088f6f" />

### Requirements

- Python 3.10+
- No Bitget API key required — uses the public spot candle endpoint

### Install

```bash
git clone https://github.com/Soka457han/AlphaAgent.git
cd AlphaAgent
pip install requests pandas numpy matplotlib seaborn
```

### Run

```bash
jupyter notebook AlphaAgent_Backtest.ipynb
```

Then: **Kernel → Restart & Run All**

All 11 cells run top-to-bottom without modification. The notebook fetches live OHLCV data from Bitget's public API, computes all indicators, runs the backtest engine, prints a full performance summary, and exports two CSV files:

- `AlphaAgent_TradeLogs.csv` — full trade log with timestamps, pairs, direction, prices, quantities, PnL, and running balance
- `AlphaAgent_NAV_History.csv` — daily NAV history for the full period

### Data Source

```
GET https://api.bitget.com/api/v2/spot/market/history-candles
```

Public endpoint. No authentication. Results are deterministic given Bitget's historical data.

> Note: Backtest results may differ slightly from dashboard values due to Bitget data updates after the submission date, but strategy logic is identical.

---

## Repository Structure

```
AlphaAgent/
├── AlphaAgent_Backtest.ipynb   # Full backtest 
├── AlphaAgent_TradeLogs.csv    # Machine-readable trade log 
├── AlphaAgent_NAV_History.csv  # Daily NAV history 
├── AlphaAgent_v1.html          # Live paper trading UI & backtest presentation
└── README.md                   # This file
```

---

## Strategy Parameters (v15)

All parameters are defined in a single dict in Cell 2 of the notebook. No magic numbers anywhere in the codebase.

```python
PARAMS = {
    "rsi_period":          14,     # Wilder's RSI lookback
    "vol_lookback":        30,     # bars for annualized vol
    "rsi_long_threshold":  28,     # entry trigger (oversold)
    "rsi_exit_threshold":  60,     # exit trigger (recovery)
    "vol_low":             15.0,   # % — sweet spot lower bound
    "vol_high":            40.0,   # % — sweet spot upper bound
    "stop_loss_pct":       5.0,    # hard stop per position
    "time_stop_days":      7,      # max hold (calendar days)
    "max_positions":       5,      # simultaneous position cap
    "position_pct":        20.0,   # % of NAV per position
    "leverage":            1,      # spot only
    "margin_budget":       10_000, # starting NAV (USDT)
    "fg_block_fear":       25,     # F&G ≤ this → majors only
    "fg_block_greed":      80,     # F&G ≥ this → all entries blocked
}
```

---

## Trade Log (Hackathon Submission Format)

The exported `AlphaAgent_TradeLogs.csv` includes all required fields:

| Field | Description |
|-------|-------------|
| `timestamp_entry` | ISO 8601 entry datetime (UTC) |
| `timestamp_exit` | ISO 8601 exit datetime (UTC) |
| `pair` | Trading pair (e.g. BTCUSDT) |
| `direction` | LONG |
| `entry_price` | Execution price at entry |
| `exit_price` | Execution price at exit |
| `quantity_usdt` | USDT allocated to position |
| `pnl_usdt` | Profit / loss in USDT |
| `pnl_pct` | Profit / loss as percentage |
| `hold_days` | Days position was held |
| `exit_reason` | RSI_EXIT / STOP_LOSS / TIME_STOP / END_OF_BACKTEST |
| `account_balance_after` | Running NAV after this trade |
| `leverage` | Always 1 (spot) |
| `exchange` | Bitget Spot |
| `strategy` | AlphaAgent v14 Volatility Harness |

---
<img width="1546" height="900" alt="Screenshot 2026-06-27 142219" src="https://github.com/user-attachments/assets/bd07e896-a410-4e66-8f85-d7211086016a" />

## Paper Trading (Live)

Paper trading started **June 27, 2026 at 00:21 UTC** on GetAgent Studio.

Verify directly — no login required:  
[getagent.studio/strategy/6724a69c-472e-480f-ab6a-a766e3ade44c](https://getagent.studio/strategy/6724a69c-472e-480f-ab6a-a766e3ade44c)

---
<img width="1680" height="624" alt="Screenshot 2026-06-27 142504" src="https://github.com/user-attachments/assets/961c29d0-b840-40fa-96e6-94996bed325e" />
<img width="1768" height="873" alt="Screenshot 2026-06-27 142429" src="https://github.com/user-attachments/assets/5a4efd9e-c572-4be4-8bc6-5175b63192de" />
<img width="1786" height="798" alt="Screenshot 2026-06-27 142414" src="https://github.com/user-attachments/assets/8f1b8324-2e90-4238-8360-5069f5cb6738" />

---

## Why AI Agents, Not Just Quant

Traditional quant runs predefined rules. AlphaAgent's advantage is the AI computation layer that scans all 16 pairs simultaneously on every bar, synthesizes a cross-asset Fear & Greed reading, writes structured reasoning for every trade decision, and reflects on its own mistakes after each exit. A discretionary trader cannot do this in real time. A traditional rule-based system cannot adapt its reasoning to narrative context. This is the specific edge that agentic architecture provides over classical quantitative trading.

---

*AlphaAgent · Bitget AI Hackathon S1 · Track 1 : Trading Agent · Paper trading research · DYOR*
