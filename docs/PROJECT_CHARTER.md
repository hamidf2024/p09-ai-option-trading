# Project Charter — Project 9: AI Option Trading (v1.1, amended 2026-10-28)

> Project 9 had no formal charter in Claude. v1.0 was written on 2026-10-28 **only from the
> decisions already recorded** in the research review (`docs/reports/P9_ML_Research_Review_and_Handoff`,
> 27 Sep 2026); the unchanged v1.0 is in `version-history/PROJECT_CHARTER_v1.0_pre-amendment.md`.
> Section 10 is the 2026-10-28 amendment.

## 1. Purpose
Find machine-learning approaches that can trade listed options profitably after realistic costs,
and design a system to test them.

## 2. Scope
- In: ML forecasting of implied volatility (IV), realized volatility (RV), IV–RV spread, option
  returns, probability of a profitable trade; regime detection; strategy selection; sizing and
  hedging; cost-aware execution; walk-forward validation.
- Out (for now): live trading with capital; autonomous trading without human review.

## 3. Primary horizon
Daily + medium term (about 1 week to 3 months). Intraday is a separate, later module.

## 4. Objective hierarchy (as agreed in the research chat)
1. Survival — CVaR, stress loss, probability of ruin
2. Risk control — max drawdown, Greeks, concentration, liquidity
3. Risk-adjusted profit — Sharpe, Sortino
4. Net profit — CAGR / total P&L after all costs
5. Efficiency — return per unit of turnover, margin, capital
6. Prediction quality — RMSE, MAE, AUC (supporting only)

## 5. Core model families
XGBoost/LightGBM, LSTM, Transformer/TFT, GNN (or CNN), PPO/deep hedging; Elastic Net as control.
The other 14 reviewed models are secondary benchmarks. Prediction and decision layers stay separate.

## 6. Frameworks
1. Adaptive ML Volatility + Relative-Value + Risk Control (main)
2. ML Multi-Strategy Regime-Switching
3. ML + Deep Hedging / RL
4. Institutional VRP / Risk-Target (benchmark)
5. Institutional Option-Overlay (benchmark)

## 7. Validation protocol
Statistical → economic → execution → crisis tests (2008, 2020, 2022). Chronological walk-forward
(e.g. train 2010–2018, validate 2019–2020, test 2021, roll yearly). No random splits.
Trade only if expected edge > spread + slippage + commission + impact (the cost gate).

## 8. Plan
See `ROADMAP.md` (8 steps from the research review).

## 9. Open question
Keep daily + medium term as the primary horizon, or prioritise intraday? (Raised as a doc comment on
28 Sep 2026; no reply recorded.)

## 10. Amendment — 2026-10-28 (competitive analysis + owner goal)

**10.1 Selection objective (replaces the ranking order in section 4 for choosing models).**
The owner's goal is: (1) maximum net profit, then (2) a higher win rate, with (3) a reasonable
number of trades. Models and strategies are therefore **ranked by net P&L after all costs, then win
rate, then trade count**. A candidate needs a minimum number of out-of-sample trades to be ranked.
The survival and risk items in section 4 (CVaR, stress loss, max drawdown, Greek and margin limits)
stay as **pass/fail gates**: a candidate that breaches them is excluded however profitable it is.
Prediction metrics remain supporting diagnostics.

**10.2 Benchmarks made concrete.** Every candidate is compared with: the Goyal–Saretto IV–RV
straddle rule; the Cboe PUT and BXM indices (institutional VRP, framework #4); SHAR for IV
forecasts; and Elastic Net.

**10.3 Build order.** Reproduce a published, simple design first (Bali et al. cross-sectional
delta-hedged option returns; Tan et al. straddle strategy with turnover regularization). The full
layered architecture (regime engine, strategy selector, RL) is added only where it raises net
profit over those reproductions.

**10.4 Costs.** Every backtest reports results at several cost levels (e.g. 0, 20, 50 bps or
25–100% of quoted spread) and trains with a turnover penalty where the model allows it.

**10.5 Horizon.** Daily + medium term stays primary (the doc comment of 28 Sep 2026 had no reply;
the competitive evidence is monthly and daily). Intraday remains a later module.

**10.6 Roadmap.** Phase 9 (items 9.1–9.10) added; see `ROADMAP.md`.
