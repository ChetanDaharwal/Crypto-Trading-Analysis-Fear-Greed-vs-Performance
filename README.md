# Crypto-Trading-Analysis-Fear-Greed-vs-Performance
Do traders perform differently when the market is fearful versus greedy?
# 📊 Crypto Trading Analysis: Fear & Greed vs Performance

> **32 on-chain accounts · 211,224 trades · $10.3M gross PnL · 2023–2025**

---

## 🗂️ Project Structure

```
.
├── historical_data_compressed.csv   # Raw zstd-compressed trade data
├── fear_greed_index.csv             # CNN Fear & Greed Index (daily, 2018–2025)
├── trading_analysis.ipynb           # ★ Full analysis notebook (Parts A–C + Bonus)
├── README.md                        # This file
└── charts/
    ├── chart1_fgi_timeline.png      # FGI timeline + volume + daily PnL
    ├── chart2_pnl_sentiment.png     # PnL / win rate / volume by sentiment
    ├── chart3_behavior_heatmap.png  # Per-account behavior × sentiment
    ├── chart4_segmentation.png      # Trader segmentation scatter + ranking
    ├── chart5_insight1.png          # Insight 1: Greed amplifies risk
    ├── chart6_insight2_coins.png    # Insight 2: Coin × sentiment sensitivity
    ├── chart7_insight3_fees.png     # Insight 3: Fee drag & taker cost
    └── chart8_clustering.png        # Bonus: KMeans trader clusters
```

---

## 📦 Data

### `historical_data_compressed.csv`
- **Format:** Zstandard (zstd) compressed CSV — decompress with `zstd -d` or via `libzstd`
- **211,224 rows** across **32 wallet addresses**
- **246 unique coins** traded on a perpetuals DEX
- **Date range:** 2023-03-28 → 2025-06-15

| Column | Description |
|--------|-------------|
| `Account` | Wallet address (0x…) |
| `Coin` | Asset traded (HYPE, BTC, ETH, @107, …) |
| `Execution Price` | Fill price |
| `Size Tokens` | Position size in tokens |
| `Size USD` | Notional value in USD |
| `Side` | BUY / SELL |
| `Direction` | Open Long / Close Long / Open Short / Close Short / … |
| `Closed PnL` | Realised PnL on closing trades (0 for opens) |
| `Fee` | Trading fee paid |
| `Crossed` | `True` = taker (market order), `False` = maker (limit order) |
| `Timestamp` | Unix epoch in milliseconds |

### `fear_greed_index.csv`
- Daily CNN Crypto Fear & Greed Index from 2018-02-01
- Values: 0 (Extreme Fear) → 100 (Extreme Greed)
- Classifications: `Extreme Fear · Fear · Neutral · Greed · Extreme Greed`

---

## ⚙️ Setup

```bash
# Python 3.10+
pip install pandas numpy matplotlib seaborn scikit-learn jupyter

# Decompress the trading data (if running outside the notebook)
zstd -d historical_data_compressed.csv -o trading_data.csv

# Launch notebook
jupyter notebook trading_analysis.ipynb
```

> **Note:** The notebook includes a pure-Python zstd decompressor via `ctypes` + `libzstd.so` — no extra Python package required.

---

## 🔬 Analysis Overview

### Part A — Data Preparation & Metrics
- zstd decompression via `libzstd` ctypes bindings
- Timestamp parsing (Unix ms → datetime)
- Feature engineering: `hour`, `dow`, `month`, `net_pnl`, `is_win`, `is_open/close`
- FGI merge by date
- Per-account aggregates: trades, volume, gross PnL, fees, win rate, fee drag, ROI
- Daily aggregates: volume, net PnL, sentiment classification

### Part B — Insights

#### B1 · FGI Timeline
Three-panel chart: FGI value coloured by regime + daily volume + daily net PnL.  
Shows the full 2023–2025 sentiment arc and how trading activity tracks it.

#### B2 · PnL vs Sentiment
Closing trades only (104,408). Compares avg PnL, win rate, and trade count across Fear / Neutral / Greed / Extreme Greed.

#### B3 · Behavior Change (Heatmaps)
32×4 heatmaps show each account's gross PnL and win rate broken down by sentiment regime — revealing which traders are sentiment-sensitive vs. consistent.

#### B4 · Trader Segmentation
Win-rate vs. net-PnL scatter (bubble = USD volume) + full net-PnL ranking of all 32 accounts.

#### Insight 1 — Greed Amplifies Volume AND Risk
14-day rolling trade volume and PnL standard deviation both spike during Greed regimes. Background is shaded by sentiment, making transitions visible.

**Implication:** Greed is not "free money" — it's amplified variance. Risk management must scale with FGI.

#### Insight 2 — Coin Sensitivity to Sentiment Varies Widely
Top 8 coins show a per-coin × sentiment heatmap. HYPE and @107 dominate PnL but are highly sentiment-sensitive. BTC/ETH are sentiment-neutral and consistent.

**Implication:** Rotate toward BTC/ETH in uncertain or Fear regimes; allocate to HYPE/altcoins only in confirmed Greed.

#### Insight 3 — Fee Drag Destroys Profits for Several Accounts
Several accounts spend >30% of gross PnL on fees. Taker (market) orders cost significantly more than maker (limit) orders, with negligible difference in win rate.

**Implication:** Switching taker → maker orders is the single highest-ROI operational change available.

### Part C — Strategy Recommendations

| # | Recommendation | Expected Impact |
|---|---------------|-----------------|
| 1 | **Sentiment-timed sizing** — reduce size 20–30% in Extreme Greed | Lower drawdown risk |
| 2 | **Maker-first discipline** — shift 50%+ of taker orders to limits | ~$25–50K fee savings/yr |
| 3 | **Coin concentration cap** — HYPE ≤ 25% in Extreme Greed | Reduce tail risk |
| 4 | **Cluster-based capital allocation** — fund Elite, pause Struggling | Better capital efficiency |
| 5 | **Regime transition monitor** — flatten on FGI regime change signals | Reduce transition whipsaws |

### Bonus — KMeans Clustering (k=4)

8-feature model: `win_rate · ROI · fee_drag · taker_pct · coins_traded · avg_size · gross_pnl · fees`

| Cluster | Label | Characteristics |
|---------|-------|----------------|
| 0 | **Elite** | High PnL, high win rate, low fee drag |
| 1 | **Efficient** | Moderate PnL, maker-heavy, low fee drag |
| 2 | **Volume** | High trade count, high volume, moderate win rate |
| 3 | **Struggling** | Negative or flat PnL, high taker %, fee-heavy |

PCA projects the 8D space into 2 components (shown in chart8). Elbow analysis confirms k=4 as the optimal cluster count.

---

## 📈 Key Results

| Metric | Value |
|--------|-------|
| Total trades | 211,224 |
| Unique accounts | 32 |
| Unique coins | 246 |
| Date range | 2023-03-28 → 2025-06-15 |
| **Gross PnL** | **$10,296,959** |
| Total fees | $245,858 |
| **Net PnL** | **$10,051,101** |
| Closing trade win rate | **83.2%** |
| Taker trade share | 60.8% |
| Top coin by PnL | @107 ($2,783,913) |
| Most-traded coin | HYPE (68,005 trades) |
| Best account (net) | `…BFED23` ($2,127,387) |
| Worst account (net) | `…A0A63B` (−$169,201) |

---

## 🧠 Technical Notes

- **Decompression:** The source file uses Zstandard (magic `0xFD2FB528`) with a duplicate 4-byte header prefix. The notebook skips the first 4 bytes before decompression.
- **Closing trades:** PnL analysis uses only rows where `Closed PnL ≠ 0` (104,408 of 211,224). Opening trades have zero PnL by definition.
- **FGI merge:** Left join on `date`; days with no FGI entry are labelled `Neutral`.
- **Win rate:** Computed on closing trades only, defined as `Closed PnL > 0`.

---

*Generated with Claude Sonnet 4.6 · Data source: on-chain DEX trades + Alternative.me Fear & Greed Index*

