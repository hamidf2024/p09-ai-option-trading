# Reference System Analysis — the "m14.fund3" options system (2026-10-29)

**Bottom line:** the screenshots show a real, well-engineered ML options workflow (checkpointed
PyTorch-style model, experiment tracking, scanner → ranking → risk → two brokers → independent exits →
dashboards). They do **not** verify any daily account return. The visible profit is $53,917 realized
over August 2026 entry cohorts, of which ≈ 85 % came from one cohort. Project 9 adopts the system's
*structure*, not its claimed returns.

Sources: 21 screenshots of a third-party system shown in a video (A1–A21), reviewed by ChatGPT in
`sources/chatgpt_conversation_ai_options_trading_research_2026-10-29.md` and its 26-page report
`docs/reports/AI_Options_Trading_Research_ChatGPT_2026-10-29.pdf`. Claude re-viewed the 14 images in
the shared chat (A8–A21) and re-checked the arithmetic below on 2026-10-29.

## 1. What the system is made of

| Part | Evidence | What it tells us |
|---|---|---|
| Dashboard "txrdash" (Vercel web app) | Pages: DL Progress, Sanity, Flow, Compute Graph, Validation, Health, Live Ranking, Positions Map, Exit Schedule, PnL Daily, Short PnL | Separate screens for training, data checks, live ranking, risk, exits and accounting |
| Model | `m14.L.fund3` (training) / `m14.fund3` (trading algo) | Named, versioned model family; second variant `m14.L.h1b` running |
| Checkpoint | `m14_5x200f_553m.pt`, step ≈ 552,710,000; status "live"; scan at 13:46 ET | PyTorch-style saved model; `553m` ≈ step counter, not parameter count |
| Experiment tracking | Weights & Biases project `m10`; metric groups `gain.t` (10 charts), `gain.v` (20), `tradability.t` (9), `init` (8); `gain.v/lift`, `lift.2+`, `lift.4+`, `gain.v/put`, `gain.v/call` | Model is judged on **gain** and **tradability**, separately for calls and puts, on train and validation splits |
| Training length | `gain.v/lift` chart runs past 2.1 G steps; value rises from ≈ 0 to ≈ 10 | Very long training; the metric improved; units unknown |
| Brokers | Alpaca and Schwab columns | Two-broker execution |
| Accounting | Rows = positions **opened** that day; realized = closed, unrealized = open; % gain = (realized + unrealized) / entry cost; times in Central Time | Cohort (trade-group) returns, not account returns |
| Header | "Acct 238 pairs · 27 entry days · live" | Meaning of "pairs" unknown |

## 2. The PnL numbers (re-checked)

**August 2026 table (A14/A17), algo m14.fund3, realized PnL by entry date:**

| Entry date | Alpaca | Schwab | Calls | Puts | Realized |
|---|---|---|---|---|---|
| 28 Aug | $0 | $0 | $0 | $0 | $0 |
| 27 Aug | +5,552 | −1,856 | −382 | +4,078 | +3,696 |
| 26 Aug | +24,070 | +21,785 | +45,855 | 0 | **+45,855** |
| 25 Aug | −2,777 | −1,303 | −4,130 | +50 | −4,080 |
| 24 Aug | +1,167 | +3,698 | +6,050 | −1,185 | +4,865 |
| 21 Aug | −5,053 | −1,178 | −2,969 | −3,262 | −6,231 |
| 20 Aug | −683 | −1,673 | +451 | −2,807 | −2,356 |
| 19 Aug | +1,874 | −162 | −3,429 | +5,142 | +1,712 |
| 18 Aug | +6,697 | −2,360 | +4,337 | 0 | +4,337 |
| 17 Aug | +4,085 | +2,033 | −5,102 | +11,220 | +6,118 |
| **Total** | **+34,932** | **+18,985** | **+40,681** | **+13,236** | **+53,917** |

- Totals reconcile (row sums are $1 lower — rounding).
- 26 Aug cohort = $45,855 / $53,917 ≈ **85 %** of profit; all other cohorts ≈ +$8,062.
- 6 cohorts positive, 3 negative, 1 flat (cohort-level only; trade-level win rate is not visible).
- Unrealized PnL, account size, fees and individual trades are not visible.

**Earlier snapshot (A18–A21, updated 8/23 12:42), 17–21 Aug cohorts:** realized $10,055 (Alpaca +$11,973,
Schwab −$1,918; calls −$3,500, puts +$13,555), unrealized −$2,474, net $7,581. Displayed total 7.7 %;
the stated formula gives ≈ 5.8 % on ≈ $130,730 implied entry cost — **unexplained**.

**Same-date figures changed between snapshots** (Schwab): 20 Aug −$2,734 → −$1,673; 19 Aug +$899 → −$162
(offsetting ±$1,061); 18 Aug −$1,419 → −$2,360; 17 Aug +$1,336 → +$2,033. Needs a trade ledger to explain.

## 3. What is and is not established

| Established | Not established |
|---|---|
| A working ML pipeline with checkpoints, experiment tracking and a live dashboard | Any daily return on total account equity (neither 1 %/day nor 5 %/day) |
| Model evaluated on gain and tradability, calls and puts separately | Architecture, features, targets, the meaning of `lift` and `5x200f` |
| Two brokers, independent exit schedule, cohort accounting | That the plotted checkpoint produced the displayed trades |
| $53,917 displayed realized August cohort profit | Live fills (Alpaca paper mode omits impact, latency, queue position) |
| Profit is concentrated in one cohort | Whether large winners recur in other periods |

## 4. What Project 9 takes from it

1. **Copy the structure:** scanner → features → model registry/checkpoints → ranking → tradability
   filter → risk controller → broker workers → independent exit service → ledger → dashboards.
2. **Train on economics:** target = executable net option PnL (dollars and % of premium), with an
   auxiliary **tradability** output and **separate call/put** heads as ablations.
3. **Audit trail:** every trade links to checkpoint file + hash, data cutoff and activation time.
4. **Two dashboard views:** calendar-day **account** return (primary) and entry-date **cohort** return
   (explanatory), each labelled with its denominator.
5. **Concentration test:** report top-day/top-cohort share of profit and whether big winners recur.

## 5. Evidence still wanted from the original source

Full video or explanation; daily net-liquidation equity from inception; deposits/withdrawals; broker
order/fill/fee export (paper vs live); position-level contract IDs and timestamps; the code for every
PnL % and total; definitions of `gain.v/lift`, thresholds, `gain.t/v`, `tradability.t`, Step and
`5x200f`; the checkpoint config and the mapping `m14.L.fund3` → `m14.fund3`; all versions including
failed ones.
