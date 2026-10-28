# Results Summary — Project 9: AI Option Trading

**There are no backtest or trading results yet.** Project 9 is at the research and design stage.
Its output so far is a reviewed study of ML models and frameworks for options trading
(`docs/reports/P9_ML_Research_Review_and_Handoff.*`, 27 Sep 2026), built from a ChatGPT research
conversation and a critical review by Claude. All star ratings below are qualitative opinions from
that conversation, not measured results.

## What the research concluded

1. **No single model wins.** The best model is the one with a stable, after-cost, out-of-sample
   edge, not the lowest prediction error.
2. **Top-ranked model families:** XGBoost/gradient-boosted trees (#1), LSTM, Transformer, LightGBM,
   TFT; ensembles/stacking for production. Six core families chosen: XGBoost, LSTM,
   Transformer/TFT, GNN, PPO/deep hedging, Elastic Net (control).
3. **Most important success factors (rated 10/10):** data quality, leakage prevention, target
   definition, transaction-cost realism, out-of-sample validation.
4. **Five frameworks:** Adaptive ML volatility relative-value (#1), regime-switching ML, ML + deep
   hedging/RL; institutional VRP and option overlay as benchmarks.
5. **Horizon:** daily + medium term primary; intraday later and separate.
6. **Architecture:** 10 layers — data → features → forecasts → regime → strategy selector →
   sizing/hedging → **cost gate** → risk limits → execution → walk-forward monitoring
   (`docs/assets/recommended_architecture.png`).

## Framework × horizon ratings (qualitative, 5 = best)

| Framework | Profit | Risk control | Intraday | Daily | Medium | Long |
|---|---|---|---|---|---|---|
| 1 Adaptive ML | 5 | 4.5 | 4.5 | 5 | 5 | 4 |
| 2 Regime-switching ML | 4.5 | 5 | 4.5 | 5 | 5 | 5 |
| 3 ML + deep hedging / RL | 5 | 5 | 4 | 5 | 5 | 4.5 |
| 4 Institutional VRP | 4 | 4.5 | 3 | 4.5 | 5 | 5 |
| 5 Institutional overlay | 3 | 5 | 2 | 4 | 4.5 | 5 |

## Issues found in the critical review

- Internal inconsistencies (e.g. Bi-LSTM called best while its quoted error is worse than LSTM).
- Almost every model scores 4–5 stars, so the ratings don't discriminate.
- Many cited 2026 papers needed checking. The competitive analysis (28 Oct 2026) opened five key
  sources and confirmed: Bali et al. (RFS, 12M+ observations, 1996–2020) and Kim & Oh (Federal
  Reserve, ~13% IV RMSE reduction).

## Published reference points (not this project's results)

| Study / index | Result |
|---|---|
| Tan, Roberts & Zohren 2024 — LSTM straddles, S&P 100, 2010–2023 | Sharpe 1.33 (no costs), 0.95 at 20 bps, 0.39 at 50 bps; 0.69 at 50 bps with turnover regularization |
| Best rules-based benchmark in the same study (TSMR) | Sharpe 0.76 (no costs), 0.51 at 50 bps |
| Cboe PUT index vs S&P 500, 1986–2015 | 10.1% vs 9.8% a year; volatility 10.1% vs 15.3%; Sharpe 0.67 vs 0.47 |

## Next result expected

Roadmap items 9.1–9.5: profit-first selection rule, data vendor, Goyal–Saretto rule benchmark, then
reproductions of Bali et al. and Tan et al.
