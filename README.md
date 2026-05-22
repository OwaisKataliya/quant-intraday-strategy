# QUANT — Intraday Equity Trading Strategy

> **Team:** Quantifiers | **Event:** Inter IIT Bootcamp  
> **Members:** Owais Kataliya, Rachit Gupta, Naman Goyal

---

## Overview

A data-driven **intraday equity trading strategy** for NIFTY 100 stocks. The system:

1. Computes a **composite Trade Potential Score (TPS)** daily to rank and select the top 8 stocks
2. Executes **long/short intraday trades** using VWAP + RSI signals
3. Manages risk with **ATR-based stop-loss and take-profit** levels
4. Backtests over a full year (Jul 2024 – Jun 2025) under realistic market conditions

---

## Strategy Architecture

### Stock Selection — Trade Potential Score (TPS)

```
TPS = 0.6 × MS + 0.3 × VS + 0.1 × LS
```

| Score | Indicators | Weights |
|---|---|---|
| **Momentum (MS)** | RSI, ADX, CCI, ROC, VWAP | 0.35 / 0.20 / 0.10 / 0.10 / 0.25 |
| **Volatility (VS)** | ATR, BB Width, HV, Daily Range, Vol Volatility | 0.35 / 0.20 / 0.15 / 0.15 / 0.15 |
| **Liquidity (LS)** | Volume Ratio, Turnover | 0.50 / 0.50 |

All indicators are min-max scaled to [0, 1] before combining.

### Entry Rules

| Signal | Condition |
|---|---|
| **Long** | Price > VWAP **and** RSI < 70 |
| **Short** | Price < VWAP **and** RSI > 30 |

### Exit Rules

- **Stop-Loss:** ATR multiplier × 1.0
- **Take-Profit:** ATR multiplier × 4.5
- **Signal Reversal:** flip long ↔ short
- **Force Close:** end of day at 3:30 PM

### Position Sizing

- 12.5% of daily portfolio per stock (8 stocks = 100% deployed)
- Capital compounds within and across days

---

## Backtest Results

| Metric | Value |
|---|---|
| Backtest Period | 1 Jul 2024 – 30 Jun 2025 |
| Initial Portfolio | ₹1,00,000 |
| Final Portfolio | ₹1,39,902.76 |
| **Net Return** | **39.90%** |
| **Sharpe Ratio** | **4.03** |
| Max Drawdown | -2.84% |
| Avg Daily Return | 0.13% |
| Total Trades | 6,800 |
| Win Rate | 35.66% |
| Long Win Rate | 36.62% (3,069 trades) |
| Short Win Rate | 34.87% (3,731 trades) |
| Avg PnL per Trade | ₹5.87 |

### Exit Breakdown

| Reason | Count |
|---|---|
| Signal Reversal | 4,486 |
| Force Close | 1,482 |
| Stop Loss | 783 |
| Take Profit | 49 |

---

## Project Structure

```
quant-intraday-strategy/
├── Bootcamp_Intraday_Strategy.ipynb   # Main notebook (data loading → backtest → analysis)
├── requirements.txt                   # Python dependencies
└── README.md
```

### Notebook Sections

1. **Data Loading** — Downloads NIFTY 100 intraday CSV data from Google Drive
2. **Indicators** — `Indicators` class with RSI, MACD, VWAP, ATR, ADX, CCI, ROC, BB Width, HV, Volume metrics
3. **Stock Selection** — `Stock_Selections()` — TPS scoring and daily top-8 selection
4. **Strategy Engine** — `run_capital_based_strategy()` — intraday signal generation + order execution
5. **Backtesting** — `run_full_backtest()` — iterates over all trading days
6. **Analysis** — Equity curve, drawdown, daily returns, NIFTY benchmark comparison

---

## Setup & Usage

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/quant-intraday-strategy.git
cd quant-intraday-strategy
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the notebook

Open `Bootcamp_Intraday_Strategy.ipynb` in Jupyter or Google Colab.

> **Note:** The notebook downloads data (~NIFTY 100 intraday CSVs) from a shared Google Drive link automatically in Cell 1. Ensure you have internet access and `gdown` installed.

### Google Colab (recommended)

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

Upload the notebook and run all cells. The data download step handles everything automatically.

---

## Dependencies

See [`requirements.txt`](requirements.txt) for the full list. Key libraries:

- `pandas`, `numpy` — data processing
- `matplotlib` — visualization
- `gdown` — Google Drive data download
- `tqdm` — progress bars

---

## Disclaimer

This project is for **educational and research purposes only**. Past backtest performance does not guarantee future results. This is not financial advice.