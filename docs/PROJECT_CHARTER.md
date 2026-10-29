# Project Charter — Project 9: AI Option Trading (v1.2, 2026-10-29)

> **v1.2 consolidates v1.1 with the 2026-10-29 review** of the ChatGPT chat "AI Options Trading
> Research" (21 screenshots of a reference ML options system and a 26-page research report; see
> `REFERENCE_SYSTEM_ANALYSIS.md`). Earlier versions: `version-history/PROJECT_CHARTER_v1.0_pre-amendment.md`
> (27 Sep research decisions) and `version-history/PROJECT_CHARTER_v1.1_pre-amendment.md`
> (28 Oct competitive-analysis amendment). Section 13 lists what changed.

## 1. Purpose
Build and rigorously test an AI options-trading system that grows **total account equity after all
trading costs**, using a reference system seen in the wild (`m14.fund3`) as the structural model and
published research as the benchmark.

## 2. Goals

| # | Goal | How it is measured |
|---|---|---|
| G1 | **Primary — net account growth.** Owner target: **> 1 % per trading day**, defined as geometric daily growth of total account equity after costs, losing days allowed | Cash-flow-adjusted daily account return; compounded growth; share of days > +1 % |
| G2 | Survive while pursuing G1 | Pass/fail gates: max drawdown, daily loss, CVaR/stress loss, Greek and margin limits (section 8) |
| G3 | Prove the edge is real, not luck or overfitting | Out-of-sample chronological windows, cost stress, deflated Sharpe / backtest-overfitting checks |
| G4 | Beat simple alternatives | Must beat no-trade, a fixed rule, Elastic Net, Goyal–Saretto, Cboe PUT/BXM under the same costs |
| G5 | Build a reproducible, auditable system | Every trade linked to checkpoint, data version and ledger entry |

**The 1 % target is a hypothesis to test, not a promise.** 1 %/day compounds to ≈ 12.3× in 252
trading days; nothing yet shows it is achievable. A strategy that passes G2–G4 but misses 1 %/day is
still a valid result and stays visible. Do not "reach" 1 % by changing the denominator, dropping losing
positions, adding leverage or reusing the final test period.

**Win rate is a diagnostic, not a goal.** The owner accepts a low win rate if winners outweigh losers.
Report it with average win/loss, losing streaks and profit concentration.

## 3. Scope

**In (first build):**
- SPY and QQQ standard options (one shared risk bucket); long calls and long puts; debit spreads as a
  second test.
- Supervised models predicting executable net option PnL, tradability and downside.
- Event-driven backtest on historical bid/ask quotes; paper trading; then a small live pilot only after
  separate authorisation.
- Track B (benchmarks): published daily/monthly designs — Bali et al. 2023, Tan et al. 2024,
  Goyal–Saretto, Cboe PUT/BXM.

**Out (for now):** uncovered (naked) short options; 0DTE; overnight holding; single stocks (until
corporate actions and earnings are handled); language models making buy/sell decisions; autonomous
live trading without human review.

## 4. Horizon and measurement
- **Measurement:** calendar-day account return is the headline number. Entry-date **cohort** returns are
  kept as an explanatory view with their own label and denominator (the reference system's dashboard
  mixes these up).
- **Track A (primary, new):** intraday-to-next-day holding on options with weeks to expiry.
- **Track B (benchmark):** daily to monthly designs from the 28 Oct competitive analysis.
- Daily measurement does **not** require daily-expiring options.

## 5. First experiment contract (pre-register before looking at the test period)

| Setting | Value (starting assumption, not known to be profitable) |
|---|---|
| Underlyings | SPY, QQQ |
| Contracts | Standard, unadjusted long calls and puts |
| Expiry | 7–21 calendar days to expiry (DTE); later grid 5–10 / 11–21 / 22–45 |
| Delta | absolute delta 0.35–0.60 |
| Decisions | every 15 minutes, after a completed feature interval |
| Holding | fixed 60 minutes; no entry that can't close before the session cutoff |
| Positions | max 1 open per underlying; integer contracts |
| Labels | unit-contract net dollar PnL **and** % of premium (exit bid − entry ask − fees − slippage) |
| Models compared | fixed momentum rule, Elastic Net, LightGBM, 2-layer neural net, one LSTM or causal-CNN |
| Tuning | budget fixed in advance; every configuration and seed logged |
| Promotion | net calendar-day account performance, drawdown, uncertainty, cost-stress stability |

## 6. Models and build order

| Order | Model family | Role |
|---|---|---|
| 1 | Fixed rules + regularized linear (Elastic Net / calibrated classifier) | Mandatory baselines |
| 2 | Gradient-boosted trees (LightGBM / XGBoost) | Primary first model |
| 3 | Small feed-forward neural net (PyTorch, checkpointed) | First neural candidate, run alongside trees |
| 4 | LSTM or causal CNN/TCN | Sequence challenger |
| 5 | Quantile / distribution models | Downside, uncertainty, abstain-or-trade |
| 6 | TFT or small transformer; GNN/CNN on the IV surface | Later, only if data justifies it |
| 7 | RL / deep hedging | Exits, execution, hedging — only after a realistic simulator exists |
| — | Language models | Optional, logged auxiliary inputs only |

**Neural ablations (from the reference system):** gain-only vs gain + **tradability** head; separate
**call/put** outputs; added downside/uncertainty output. Ensembles only when members add distinct
out-of-sample information; weights learned on training/validation data, never on the test period.

Prediction and decision layers stay separate (unchanged from v1.0).

## 7. System architecture
Raw data store → contract master → feature builder → label builder → **model registry (checkpoints,
hashes, trial log)** → ranking + tradability filter → **portfolio/risk controller** (can veto any
model) → order service (idempotent, one broker first) → **independent exit service** → **ledger**
(cash, fills, exercises, assignments, fees) → monitoring and dashboards (account view + cohort view).
CPU is enough to start; no large GPU purchase.

The 10-layer research architecture from the 27 Sep review (`assets/recommended_architecture.png`) stays
as the long-term target; the regime engine, strategy selector and RL are added only where they raise
net account return over the first build.

## 8. Risk controls (pilot starting points — owner to confirm)

| Control | Starting value |
|---|---|
| Max contractual loss per new position | 0.25–0.50 % of equity (full premium, not the stop) |
| Total open premium / defined-risk loss | ≤ 5 % of equity, including pending orders |
| Same or correlated underlying (SPY+QQQ) | ≤ 2 % of equity |
| Daily loss trigger | ≈ 1–1.5 % of opening equity → stop new entries, reduce risk |
| Drawdown review | ≈ 5 % from high-water mark → pause and investigate |
| Overnight exposure | Off initially |

These limits may make 1 %/day unreachable (5 % of equity deployed needs a 20 % premium gain for 1 %).
That tension is deliberate and must be resolved by evidence, not by loosening limits.
Never size up to recover losses or to force a daily target.

## 9. Validation protocol
- Chronological, nested walk-forward (e.g. train 2018–2021, validate 2022, test 2023, roll yearly);
  preprocessing and tuning fitted only on data available at the time.
- Purge overlapping labels; embargo gaps; keep same-day contracts together; include expired contracts.
- Execution sim: buy at post-decision ask, sell at post-exit bid, latency, partial fills, fees, stale or
  crossed quotes, early closes; spreads as complex orders, never assumed mid fills.
- Cost stress at several levels (0 / 20 / 50 bps or 25–100 % of spread); turnover penalty where possible.
- Block-bootstrap of daily account returns; Deflated Sharpe and probability-of-overfitting checks.
- Ablations and label-shuffle sanity checks; report every window, including failures.
- Crisis periods tested separately (2008 and 2020 where data allows, 2022).

## 10. Data and brokers
- **Data candidates** (sample-compare before buying): Cboe DataShop option quotes (from 2012), Massive
  options flat files, Databento OPRA (from 2013; pre-28 Mar 2023 data differ), OptionMetrics IvyDB
  (end-of-day from 1996, for Track B), Alpaca historical options (from Feb 2024, indicative feed).
- **Brokers:** Alpaca and Schwab (both have option APIs, as in the reference system). Start with **one**
  broker; Alpaca paper results don't include impact, latency or queue position, so a small live pilot is
  needed to measure real fills.
- Check FINRA's new intraday-margin rules (effective 4 Jun 2026, firm transition to 20 Oct 2027) with the
  chosen broker.

## 11. Success report card
Beginning/ending equity with cash flows; gross vs net PnL with costs split out; arithmetic and geometric
daily return; share of days > +1 %, losing days, worst day; max drawdown, recovery time, expected
shortfall; exposure-adjusted benchmarks; rolling and regime results; **top-day and top-trade profit
concentration** (the reference system made ≈ 85 % of displayed profit in one cohort); capacity, fill
rate, slippage, latency; all-in operating cost; win rate as a diagnostic.

## 12. Decisions needed from the owner
1. Starting account size and the capital for this strategy.
2. Maximum acceptable drawdown and daily loss.
3. Broker to start with (Alpaca or Schwab) and option approval level.
4. Historical-data budget and vendor.
5. Precise success thresholds (e.g. what counts as passing G1).
6. Confirm Track A (intraday-to-next-day, SPY/QQQ) as the primary build, replacing v1.1's
   "daily + medium term primary".
7. Intraday only, or overnight holding later.

## 13. Changes from v1.1

| Area | v1.1 (28 Oct) | v1.2 (29 Oct) |
|---|---|---|
| Headline goal | Max net profit, then win rate, then trade count | Net **account** growth, target > 1 %/day (hypothesis); win rate diagnostic only |
| Primary horizon | Daily + medium term; intraday later | Track A intraday-to-next-day (SPY/QQQ) primary; Track B daily/monthly benchmarks — **owner to confirm** |
| First build | Reproduce Bali et al. and Tan et al. | First experiment contract (section 5); reproductions move to Track B |
| Model order | XGBoost, LSTM, Transformer, GNN, RL, Elastic Net | Rules/linear → trees → small NN → LSTM/TCN → quantile; TFT/GNN/RL later |
| Neural targets | IV, RV, returns, P(profit) | Executable net option PnL + tradability + call/put + downside |
| Architecture | 10-layer research design | Adds model registry, risk veto, independent exit service, ledger, dual dashboards |
| Risk | Gates named | Pilot limits quantified (section 8) |
| Out of scope | Live trading, autonomy | Adds naked short options, 0DTE, overnight, single stocks, LLM trade decisions |
| Data/brokers | Vendor TBD | Five vendors and two brokers assessed; start with one broker |
