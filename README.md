# Trader Performance vs Market Sentiment

Analysis of how Bitcoin Fear/Greed sentiment relates to trader behavior and performance on
Hyperliquid. Built for the Primetrade.ai Data Science Intern Round-0 assignment.

**Start here:** [`analysis.ipynb`](analysis.ipynb) — the full analysis, with charts and printed
tables already saved in the notebook, so it reads top-to-bottom on GitHub without needing to run
anything. [`WRITEUP.md`](WRITEUP.md) has the condensed 1-page summary of methodology, insights,
and strategy recommendations.

## Repo structure

```
.
├── analysis.ipynb            # Main notebook: Parts A, B, C + bonus predictive model
├── WRITEUP.md                 # 1-page summary: methodology, insights, strategy recs
├── README.md                  # This file
├── requirements.txt
├── charts/                    # PNG exports of every chart in the notebook
│   ├── fig1_performance_by_sentiment.png
│   ├── fig2_behavior_by_sentiment.png
│   ├── fig3_segment_size.png
│   ├── fig4_segment_frequency.png
│   ├── fig5_segment_consistency.png
│   ├── fig6_trader_archetypes.png
│   └── fig7_cumulative_pnl_timeline.png
└── data/
    ├── raw/                   # Original source files, unmodified
    │   ├── fear_greed_index.csv
    │   └── historical_data.csv
    └── processed/              # Output tables produced by the notebook
        ├── daily_trader_metrics.csv   # per-trader-per-day metrics + sentiment + segments
        └── account_summary.csv        # per-account summary + segment labels
```

## Datasets

1. **Bitcoin Fear & Greed Index** (`data/raw/fear_greed_index.csv`) — daily sentiment
   classification (Extreme Fear → Extreme Greed), 2018–2025.
2. **Hyperliquid historical trades** (`data/raw/historical_data.csv`) — ~211k trade-level fills
   across 32 accounts, spanning May 2023–May 2025 (trade-level fields: account, coin, execution
   price, size, side, direction, closed PnL, fees, etc.).

## Setup & how to run

```bash
# 1. Create a virtual environment (optional but recommended)
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch the notebook
jupyter notebook analysis.ipynb
# then Run All — everything (metrics, charts, model) regenerates from the raw CSVs in data/raw/
```

The notebook is self-contained: it reads only from `data/raw/`, derives all metrics and segments
itself, and regenerates every chart in `charts/` when re-run.

## Methodology at a glance

- **Alignment:** trade timestamps parsed to dates and matched 1:1 against the daily sentiment
  file (479/480 trading days matched directly; the 1 missing day is forward-filled from the
  prior day's regime).
- **Metrics:** built at the trader-day level — realized PnL, win rate, average trade size, trade
  count, long/short trade ratio — then rolled up to per-account summaries for segmentation.
- **Segments:** accounts split into terciles on three independent axes — trade size (risk
  proxy), trading frequency, and PnL consistency (mean ÷ std of daily PnL).
- **Note:** the raw export has no explicit leverage/margin field, so average trade size (USD) is
  used throughout as a transparent, disclosed proxy for position-sizing risk.

See [`WRITEUP.md`](WRITEUP.md) for the full set of insights and strategy recommendations, and
`analysis.ipynb` for all supporting code, statistical tests, and charts.
