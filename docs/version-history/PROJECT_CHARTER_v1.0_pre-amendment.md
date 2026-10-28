# Project Charter — Project 9: AI Option Trading (v1.0, baseline)

> Project 9 had no formal charter in Claude. This v1.0 was written on 2026-10-28 **only from the
> decisions already recorded** in the research review (`docs/reports/P9_ML_Research_Review_and_Handoff`,
> 27 Sep 2026). It is kept unchanged here as the pre-amendment baseline.

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
See `ROADMAP_v1.0_pre-amendment.md` (8 steps from the research review).

## 9. Open question
Keep daily + medium term as the primary horizon, or prioritise intraday? (Raised as a doc comment on
28 Sep 2026; no reply recorded.)
