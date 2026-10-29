# Project 9 — AI Option Trading (ML research)

Private archive of Claude Project 9, "Project 9: AI Option Trading" (created 27 Sep 2026).

**Stage:** research and design (charter v1.2, 29 Oct 2026). No data, code or backtests exist yet.

**Goal (owner):** grow total account equity after all costs — target **> 1 % per trading day**,
treated as a hypothesis to test. Win rate is a diagnostic; survival limits are pass/fail gates.

**Approach:** copy the *structure* of a reference ML options system (`m14.fund3`: checkpointed model,
gain + tradability metrics, risk controller, independent exits, ledger) and test a first experiment on
SPY/QQQ options, benchmarked against published research (Bali et al., Tan et al., Cboe PUT/BXM).

## What's here

```
docs/
  PROJECT_CHARTER.md          v1.2 (29 Oct 2026; goals, scope, first experiment, risk, changes vs v1.1)
  ROADMAP.md                  v1.2 (Phase 10 = main build path; Phase 9 = benchmark track)
  REFERENCE_SYSTEM_ANALYSIS.md  what the m14.fund3 screenshots show and don't show (29 Oct 2026)
  COMPETITIVE_ANALYSIS.md     published research, institutional benchmarks, platforms; gaps; additions
  reports/
    P9_ML_Research_Review_and_Handoff.docx / .pdf / .md   ML research review (27 Sep 2026)
    AI_Options_Trading_Research_ChatGPT_2026-10-29.pdf     ChatGPT's 26-page report on the reference system and plan
  assets/recommended_architecture.png                     10-layer system diagram from the review
  version-history/            v1.0 and v1.1 charter and roadmap
results/
  RESULTS_SUMMARY.md          findings so far (research only) + published reference points
sources/
  chatgpt_conversation_project_option_trading.txt         the source ChatGPT conversation as captured
  chatgpt_handoff_options_research.docx / .md             ChatGPT's own handoff file (self-described as incomplete)
  chatgpt_conversation_ai_options_trading_research_2026-10-29.md   second ChatGPT chat (reference-system screenshots)
  chatgpt_ai_options_trading_research_report_2026-10-29.txt        text copy of the 26-page report
chats/
  claude_chat_transcript.md   the Claude conversation in this project
  raw/claude-export-p09.json.gz                           full export incl. every tool input/output
project/
  project_metadata.json       project info, doc link, the open doc comment
MANIFEST.sha256               checksum of every file
```

## Key decisions so far

- Six core model families: XGBoost, LSTM, Transformer/TFT, GNN, PPO/deep hedging, Elastic Net (control).
- Primary horizon: daily + medium term; intraday later.
- Trade only when expected edge clears spread + slippage + commission + impact (the cost gate).
- Amendment (2026-10-28): survival/risk limits are pass/fail gates; reproduce Bali et al. (2023) and
  Tan et al. (2024) — now the benchmark track.
- v1.2 (2026-10-29): headline metric = calendar-day account return after costs (not cohort % or
  model "lift"); first experiment = SPY/QQQ long calls/puts, 7–21 DTE, |delta| 0.35–0.60, 15-min
  decisions, 60-min hold; models in order rules/linear → LightGBM → small NN → LSTM/TCN; no naked
  short options, 0DTE or overnight at first.

## Open decisions (charter §12)

Account size; max drawdown and daily loss; starting broker (Alpaca or Schwab); data budget and vendor;
success thresholds; confirm intraday-to-next-day Track A as primary (replaces "daily + medium term");
intraday-only or overnight later.

## Disclosure

Research only. Not investment advice. Nothing here shows that any strategy is profitable.
