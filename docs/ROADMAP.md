# Roadmap — Project 9: AI Option Trading (v1.2, 2026-10-29)

> v1.0 (27 Sep research review) and v1.1 (28 Oct, Phase 9 from the competitive analysis) are in
> `version-history/`. v1.2 adds **Phase 10**, the staged build from the 29 Oct ChatGPT report and the
> reference-system review (`REFERENCE_SYSTEM_ANALYSIS.md`), and makes it the main path.

## Main path — Phase 10: build and test the system (Track A)

| Stage | Work | Output to review | Advance only when | Status |
|---|---|---|---|---|
| 10.0 | Review reference system (21 screenshots) and ChatGPT's 26-page report; update charter | `REFERENCE_SYSTEM_ANALYSIS.md`, charter v1.2 | — | **Done** (29 Oct 2026) |
| 10.1 | **Definition and audit:** owner decisions (charter §12), return denominator, universe, horizon, risk limits, success thresholds | Signed-off experiment contract | Accounting and success criteria are unambiguous | Not started |
| 10.2 | **Data sample:** compare small matched samples from 2–3 vendors (normal day, volatile day, early close, expiry); build contract master | Data quality and timing report | Historical contracts and executable quotes can be rebuilt | Not started |
| 10.3 | **Baselines:** labels + event-driven simulator; fixed momentum rule, Elastic Net, LightGBM | Chronological results with cost sensitivity | Net edge survives realistic costs | Not started |
| 10.4 | **Challengers:** 2-layer NN (gain-only vs + tradability, call/put heads), LSTM or causal CNN, quantile model; constrained ensemble | Trial registry, ablations, calibrated forecasts | Gains survive new windows and multiple-testing adjustment | Not started |
| 10.5 | **Shadow / paper mode:** live signals, paper orders, deliberate failure tests | Reconciled ledger and exception log | No unexplained position/cash mismatches or duplicate orders | Not started |
| 10.6 | **Small live pilot** (separate authorisation, small defined risk) | Real fill and slippage evidence | Real economics support continuing | Not started |
| 10.7 | **Gradual scale-up** in measured steps | Capacity and risk review per step | Edge persists within the risk budget | Not started |

Planning estimate: a focused research prototype (10.1–10.4) in roughly **6–12 weeks** with data and
engineering in place; shadow and live evidence take longer. First deliverable is a reproducible research
package (data dictionary, frozen splits, label spec, trial log, simulator assumptions, account-return
calculation, exception report) — dashboards come after.

**Optional:** ask the reference-system owner for the evidence listed in `REFERENCE_SYSTEM_ANALYSIS.md` §5.

## Benchmark path — Track B (from v1.1, Phase 9)

| Item | Title | Status |
|---|---|---|
| 9.1 | Model-selection rule — **revised in v1.2:** net account return first; survival limits as pass/fail gates; win rate diagnostic | Superseded by charter v1.2 §2 |
| 9.2 | Choose data vendor; lock a point-in-time dataset | Merged into 10.2 |
| 9.3 | Rule benchmark: Goyal–Saretto IV–RV straddle sort, net of spread costs | Not started |
| 9.4 | Reproduce Bali et al. (2023): monthly delta-hedged option returns, tree/NN models | Not started |
| 9.5 | Reproduce Tan et al. (2024): LSTM/linear straddles with turnover regularization at 0/20/50 bps | Not started |
| 9.6 | IV-forecast baseline: tree-based SHAR vs SHAR | Not started |
| 9.7 | Compare every strategy with Cboe PUT and BXM over the same period | Not started |
| 9.8 | Deep hedging only after a cost-aware simulator exists | Not started (after 10.3) |
| 9.9 | Multiple-testing control (deflated Sharpe / reality check) | Merged into 10.4 |
| 9.10 | Paper-trade the winner via a broker-connected bot before any capital | Merged into 10.5 |

## Earlier research phases (v1.0)

| Phase | Item | Status |
|---|---|---|
| 0 | Research review: 20 models, 20 success factors, 5 frameworks, horizon fit, architecture | **Done** (27 Sep 2026) |
| 1 | Verify key papers and numbers | Partly done (5 sources checked 28 Oct; report of 29 Oct checked Bali, Tan, Buehler, Bailey) |
| 2 | "Top 20 option strategies" study; strategy × model grid | Not started — narrowed: first grid is DTE × delta × holding time (charter §5) |
| 3 | Data vendor and dataset | Merged into 10.2 |
| 4 | Targets and regime thresholds | Targets set in charter §5; regimes later |
| 5 | Baselines | Merged into 10.3 |
| 6 | XGBoost/LightGBM, then LSTM | Merged into 10.3–10.4 |
| 7 | Regime engine, strategy selector, Transformer/GNN, RL | After 10.4, only if it adds net return |
| 8 | Multiple-testing corrections; paper trade | Merged into 10.4–10.5 |

**Next step:** 10.1 — answer the seven owner decisions in charter §12.

**Progress:** 2 items done (Phase 0, 10.0); main path 1 of 8 stages done.
