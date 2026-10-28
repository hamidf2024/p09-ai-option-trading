# Competitive Analysis — Project 9 (AI Option Trading)

Prepared 2026-10-28. Purpose: compare this project with the published research, institutional
benchmarks and retail platforms it competes with. Then identify what to add so the project meets the
owner's goal: **(1) maximum net profit, then (2) a higher win rate, with (3) a reasonable number of
trades.**

**Status of this project:** research and design only. It has a reviewed model and framework study
(see `docs/reports/`) but no dataset, no code and no backtest yet. Everything below is therefore a
comparison of *plans* against *published results*.

Reported figures are the authors' or vendors' own. They come from different markets, periods and
cost assumptions, so they are context, not like-for-like comparisons.

## 1. Competitive landscape

| Competitor | Type | What it does / reports | Relevance to this project |
|---|---|---|---|
| **Bali, Beckmeyer, Mörke & Weigert (2023)**, *Option Return Predictability with Machine Learning and Big Data* (Review of Financial Studies) | Published research | 12M+ US delta-hedged option returns, 1996–2020, 270 option- and stock-based characteristics. Nonlinear ML beats linear models; monthly option returns are more predictable than stock returns (out-of-sample R² above 2.5%); long-short portfolios stay profitable after conservative transaction costs. Option characteristics matter most; stock characteristics add a lot on top. Predictability is higher where arbitrage is costly (low institutional ownership, low analyst coverage). | The strongest evidence behind this project's Framework #1. Shows the profit comes from **cross-sectional ranking of many underlyings' options**, monthly, delta-hedged. That is a concrete, reproducible design the project does not have yet. |
| **Goyal & Saretto (2009)**, *Cross-Section of Option Returns and Volatility* (JFE) | Published research | Sorts stocks on the gap between historical realized and at-the-money implied volatility; long-short straddle portfolios earn large monthly returns that survive realistic spread costs (OptionMetrics, 1996–2005). | A simple **rule-based IV–RV benchmark**. This project's ML models should beat it after costs before any complexity is added. |
| **Tan, Roberts & Zohren (2024)**, *Deep Learning for Options Trading: An End-to-End Approach* | Published research | S&P 100 equity options, monthly ATM delta-neutral straddles, 2010–2023 expanding window. Sharpe (15% vol target, no costs): LSTM 1.33, Linear 1.29, MLP 0.90, CNN 0.55 vs best rules-based (TSMR) 0.76. With costs: LSTM 0.95 at 20 bps but 0.39 at 50 bps (below TSMR 0.51); **turnover regularization** lifts LSTM to 0.69 at 50 bps. | Direct peer for the LSTM leg. Confirms the project's "cost gate" idea and shows the edge disappears at high costs unless turnover is penalized in training. A linear model came close to the LSTM. |
| **Kim & Oh (2026)**, *Capturing Heterogeneity: ML Approaches to Implied Volatility Forecasting* (Federal Reserve FEDS 2026-049) | Published research | S&P 500 option IV surface. A boosted, tree-based extension of the SHAR model, split by moneyness and maturity, has the lowest error at all horizons: about 13% lower one-month-ahead RMSE than SHAR; biggest gains in stress periods. | Supports XGBoost/LightGBM as the first IV-forecasting model and the daily/medium horizon choice. Gives a published benchmark (SHAR) to beat. |
| **Buehler, Gonon, Teichmann & Wood (2019)**, *Deep Hedging* (Quantitative Finance) | Published research | Deep RL hedging of derivative portfolios with transaction costs, liquidity limits and convex risk measures; beats the complete-market (Black-Scholes style) hedge in a Heston market with costs and on S&P 500 data. | Foundation for this project's Framework #3 (ML + deep hedging/RL). Needs a realistic simulator first, so it comes late in the roadmap. |
| **Cboe S&P 500 PutWrite Index (PUT)** | Institutional benchmark index | Systematic cash-secured put selling. 1986–2015: 10.1% a year vs 9.8% for the S&P 500, with 10.1% vs 15.3% volatility (Sharpe 0.67 vs 0.47). 2006–2015: 6.6% vs 7.1%, max drawdown −33% vs −51%. Gross of costs. | A free, published proxy for the project's **Framework #4 (institutional volatility risk premium)**. The ML system has to beat this on net profit, not only on risk. |
| **ORATS** | Retail/pro options data + backtesting platform | End-of-day option data since 2007 for 5,000+ symbols; 65M+ pre-run backtests, 37 performance metrics, an overfitting check. No ML described. | Candidate **data vendor** and a ready benchmark tool for rule-based strategies. |
| **Option Alpha** | Retail backtesting + automation platform | 3 years of 1-minute option data (SPX, SPY, QQQ, IWM, XSP); turns a backtest into a bot that trades through tastytrade, TradeStation, Tradier or Schwab; paper trading. Rule-based, no ML described. | Shows the "backtest → paper → live" path retail traders already have. A practical route for the project's intraday module and paper trading. |
| **Option Omega** | Retail backtesting platform | 1-minute option data, 10+ year backtests, highly customizable rules; limited tickers. No ML described. | Same: a rule-based comparator for intraday and 0DTE ideas. |

## 2. Where we stand vs. competitors

| Capability | This project | Competitors / literature | Gap |
|---|---|---|---|
| Evidence | Literature review only; 0 backtests | Peer-reviewed backtests over 10–25 years (Bali; Goyal & Saretto; Tan et al.) | **Biggest gap.** No result of our own yet. |
| Data | Not chosen | OptionMetrics (academic standard); ORATS since 2007; 1-minute data on Option Alpha / Option Omega | Pick a vendor and lock a point-in-time dataset |
| Design | Layered: forecast → regime → strategy → sizing → cost gate → risk | Published winners are simpler: one model ranking many options monthly | Start with the simple published design, then add layers only if they add net profit |
| Universe | Not defined | Thousands of US equity options (Bali); S&P 100 (Tan et al.); S&P 500 index options (Kim & Oh) | Define it; cross-sectional is where the profit evidence is |
| Models | 20 reviewed; 6 core families chosen | Nonlinear ML (trees, NN) beats linear; LSTM ≈ linear in Tan et al. | On par in thinking; nothing built |
| Costs | "Cost gate" rule agreed | Explicit bps sensitivity and turnover regularization (Tan et al.); spread fractions (Goyal & Saretto) | Add both to the backtest from day one |
| Benchmarks | Institutional VRP and overlay named | Cboe PUT/BXM indices; SHAR for IV; Goyal–Saretto rule | Use these as the concrete benchmarks |
| Objective | ChatGPT hierarchy: survival → risk → risk-adjusted → net profit | Papers report Sharpe and long-short returns | Re-ordered to the owner's goal (profit → win rate → trades) with survival limits as hard gates (charter amendment) |
| Execution | Not planned in detail | Option Alpha: backtest → bot → broker; paper trading | Add a paper-trading step before any capital |

## 3. Roadmap additions (Phase 9, items 9.1–9.10)

Added to `docs/ROADMAP.md` and the charter's amendment section on 2026-10-28.
All are **not started**.

| Item | Title | Why (competitor / source) |
|---|---|---|
| 9.1 | Profit-first model-selection rule: net P&L → win rate → trade count, with a minimum trade count and survival limits as pass/fail gates | Owner goal |
| 9.2 | Choose the data vendor (OptionMetrics/WRDS, ORATS, Cboe DataShop) and lock a point-in-time dataset | ORATS; academic use of OptionMetrics |
| 9.3 | Rule benchmark: Goyal–Saretto IV–RV straddle sort, net of spread costs | Goyal & Saretto 2009 |
| 9.4 | Reproduce the Bali et al. design: cross-sectional, monthly, delta-hedged option returns with tree/NN models and option + stock features | Bali et al. 2023 |
| 9.5 | Reproduce the Tan et al. LSTM/linear straddle strategy with turnover regularization; report at 0, 20 and 50 bps | Tan, Roberts & Zohren 2024 |
| 9.6 | IV-forecasting baseline: boosted tree-based SHAR vs SHAR | Kim & Oh 2026 |
| 9.7 | Institutional benchmark: compare every strategy with the Cboe PUT and BXM indices over the same period | Cboe PutWrite study |
| 9.8 | Deep hedging (Framework #3) only after a cost-aware simulator exists | Buehler et al. 2019 |
| 9.9 | Multiple-testing control (deflated Sharpe / reality check) for the strategy × model grid | Review gap; 20 × 20 grid |
| 9.10 | Paper-trade the winner through a broker-connected bot before any capital | Option Alpha / broker automation |

Suggested order for the profit goal: 9.1 → 9.2 → 9.3 → 9.4 → 9.5. Publish a simple, reproducible
edge first; the layered architecture comes after it beats the benchmarks net of costs.

## 4. Implications for the website

The project page follows the same structure as Projects 1–5:
1. stat strip;
2. what was studied, ranked by the owner's priority;
3. model and framework findings;
4. Benchmark;
5. approach (architecture);
6. roadmap;
7. disclosures.

## Sources

- Bali, Beckmeyer, Mörke & Weigert, Option Return Predictability with Machine Learning and Big Data (RFS 2023) — https://academic.oup.com/rfs/article/36/9/3548/7056660 ; summary — https://alphaarchitect.com/using-machine-learning-to-predict-options-returns/
- Goyal & Saretto, Cross-Section of Option Returns and Volatility (JFE 2009) — https://www.cis.upenn.edu/~mkearns/finread/CrossOptions.pdf
- Tan, Roberts & Zohren, Deep Learning for Options Trading: An End-To-End Approach (2024) — https://arxiv.org/html/2407.21791v1
- Kim & Oh, Capturing Heterogeneity: Machine Learning Approaches to Implied Volatility Forecasting (FEDS 2026-049) — https://www.federalreserve.gov/econres/feds/capturing-heterogeneity-machine-learning-approaches-to-implied-volatility-forecasting.htm
- Buehler, Gonon, Teichmann & Wood, Deep Hedging (Quantitative Finance 2019) — https://ideas.repec.org/a/taf/quantf/v19y2019i8p1271-1291.html
- Performance of Cboe PutWrite Indexes (summary of the Cboe study) — https://www.cxoadvisory.com/equity-options/performance-of-cboe-putwrite-indexes
- ORATS, Best options backtesting platforms (vendor comparison) — https://orats.com/blog/best-options-backtesting-platforms-in-2024
- Option Alpha backtester — https://optionalpha.com/backtester
