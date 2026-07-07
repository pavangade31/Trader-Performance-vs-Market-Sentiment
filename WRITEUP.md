# Write-up: Trader Performance vs Market Sentiment

## Methodology

Two datasets were aligned by date: 32 Hyperliquid accounts' trade-level history (~211k fills,
May 2023–May 2025) and the daily Bitcoin Fear & Greed Index. Trade timestamps were parsed and
matched against sentiment (479/480 trading days matched directly; the one gap was forward-filled
from the prior day's regime, since sentiment regimes persist over multiple days). Both source
files were clean — no missing values or duplicate rows in either.

Trade fills were rolled up into **per-trader-per-day metrics**: realized PnL, win rate, average
trade size, trade count, and long/short trade ratio. The 5-level sentiment scale was grouped into
Fear / Neutral / Greed for the core comparisons. Accounts were then segmented into terciles on
three independent axes: **trade size** (risk-taking proxy — the raw data has no explicit leverage
field, so average trade size in USD stands in for it, disclosed wherever used), **trading
frequency** (active days), and **consistency** (mean ÷ std of daily PnL, a Sharpe-like stability
score).

## Insights

**1. Fear days aren't lower-PnL on average, but they are riskier.** Mean and median daily PnL are
statistically indistinguishable between Fear and Greed (Welch's t-test, p ≈ 0.47) — a few very
large trader-days swing the mean either way. But the **share of loss-making trader-days is ~60%
higher in Fear** (12.0% vs 7.6%), and **average trade size is significantly larger in Fear**
(p = 0.036). Fear regimes carry more dispersion, not necessarily worse expected value.

**2. This cohort trades "long the fear," not "risk-off."** The long:short trade ratio nearly
doubles from 1.45 in Greed to 2.87 in Fear, alongside more trades per trader-day (105 vs 77).
That's consistent with active dip-buying/accumulation behavior, not defensive de-risking —
worth flagging since it cuts against the "fear = get defensive" intuition.

**3. The Fear-day edge is concentrated in specific segments, not universal.** High-size traders
earn far more in Fear ($9,847/day) than Greed ($2,640/day), and Infrequent, high-conviction
traders post their best numbers of any segment in Fear ($22,077/day). Low-size traders show the
**opposite** pattern ($599 in Fear vs $2,910 in Greed) — they appear to get whipsawed by the same
volatility that rewards the larger/more selective accounts. A single sentiment-based rule applied
to everyone would therefore help some traders and hurt others.

## Strategy recommendations

**1. Size up for conviction traders in Fear, size down for reactive traders in Fear.** Route the
same sentiment signal to opposite sizing actions depending on segment: High-size and Infrequent
accounts have historically captured outsized PnL specifically on Fear days, while Low-size
accounts have historically done better easing off during Fear rather than staying at Greed-day
activity levels.

**2. Add a leverage/size governor on Fear days for everyone except the proven-Consistent tier.**
The Moderate-consistency tier had its best average PnL in Fear ($10,703/day) but also its highest
loss-day rate in Fear (19%, vs 4–7% in Greed) — an attractive average masking much wider
dispersion. Only accounts with an already-demonstrated consistent edge should lean into that
Fear-day average; everyone else should cap sizing rather than chase it.

## Bonus: predictive signal check

A lightweight logistic regression / random forest (temporal train/test split, no future leakage)
predicting next-day trader profitability from today's sentiment + behavior reached **AUC ≈ 0.60**
— a real but modest edge. Feature importances placed a trader's own current-day behavior (trade
count, trade size, PnL, volume) well above the sentiment features themselves. This supports the
insights above: **sentiment's effect on outcomes looks mostly indirect**, working through the
position-sizing and frequency changes it triggers in traders, rather than a direct predictor of
next-day results on its own.

## Limitations

Only 32 accounts, several of which dominate total PnL — segment-level findings are directional
hypotheses worth validating on a larger account base, not settled conclusions. Sentiment is a
single market-wide daily label, not asset-specific or intraday.
