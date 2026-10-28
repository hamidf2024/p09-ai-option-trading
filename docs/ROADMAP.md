# Roadmap — Project 9: AI Option Trading (v1.1, amended 2026-10-28)

> v1.0 built on 2026-10-28 from the "Next steps" of the research review (27 Sep 2026). v1.1 adds Phase 9 from the competitive analysis. The v1.0 copy is in `version-history/`.

| Phase | Item | Status |
|---|---|---|
| 0 | Research review: 20 models, 20 success factors, 5 frameworks, horizon fit, architecture | **Done** (27 Sep 2026) |
| 1 | Verify the key papers and numbers flagged in the review | Not started |
| 2 | Bring in the "top 20 option strategies" study; define the strategy × model grid | Not started |
| 3 | Choose a data vendor and lock the dataset (underlyings, dates, frequency, bid/ask quality) | Not started |
| 4 | Define targets (IV change, RV, IV–RV, P(P&L>0), expected P&L) and regime thresholds | Not started |
| 5 | Baselines: Elastic Net, institutional VRP rule, simple overlay | Not started |
| 6 | XGBoost/LightGBM, then LSTM; walk-forward with realistic costs and the cost gate | Not started |
| 7 | Regime engine and strategy selector; then Transformer/GNN; then RL for sizing and hedging | Not started |
| 8 | Multiple-testing corrections; paper-trade before any capital | Not started |

## Phase 9 — Competitor-driven additions (amendment, 2026-10-28)

Source: `docs/COMPETITIVE_ANALYSIS.md`. All not started.

| Item | Title | Why |
|---|---|---|
| 9.1 | Profit-first model-selection rule: net P&L → win rate → trade count (minimum trade count), survival limits as pass/fail gates | Owner goal |
| 9.2 | Choose data vendor (OptionMetrics/WRDS, ORATS, Cboe DataShop); lock a point-in-time dataset | ORATS; OptionMetrics in academic studies |
| 9.3 | Rule benchmark: Goyal–Saretto IV–RV straddle sort, net of spread costs | Goyal & Saretto 2009 |
| 9.4 | Reproduce Bali et al.: cross-sectional monthly delta-hedged option returns, tree/NN models, option + stock features | Bali et al. 2023 (RFS) |
| 9.5 | Reproduce Tan et al. LSTM/linear straddle strategy with turnover regularization at 0/20/50 bps | Tan, Roberts & Zohren 2024 |
| 9.6 | IV-forecasting baseline: boosted tree-based SHAR vs SHAR | Kim & Oh 2026 (Federal Reserve) |
| 9.7 | Compare every strategy with Cboe PUT and BXM indices over the same period | Cboe PutWrite study |
| 9.8 | Deep hedging only after a cost-aware simulator exists | Buehler et al. 2019 |
| 9.9 | Multiple-testing control (deflated Sharpe / reality check) for the grid | Review gap |
| 9.10 | Paper-trade the winner via a broker-connected bot before any capital | Option Alpha-style automation |

**Suggested order for the profit goal:** 9.1 → 9.2 → 9.3 → 9.4 → 9.5, then continue with phases 4–8.
Phase 1 (verify papers) is partly covered by the competitive analysis, which opened and checked
five of the key sources.

**Progress:** 1 of 19 items done (Phase 0).
