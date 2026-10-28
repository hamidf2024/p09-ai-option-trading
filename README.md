# Project 9 — AI Option Trading (ML research)

Private archive of Claude Project 9, "Project 9: AI Option Trading" (created 27 Sep 2026).

**Stage:** research and design. No data, code or backtests exist yet.

**Goal (owner):** find the trading model with **maximum net profit first**, then a higher win rate,
with a reasonable number of trades.

## What's here

```
docs/
  PROJECT_CHARTER.md          v1.1 (amended 2026-10-28; section 10 = amendment)
  ROADMAP.md                  v1.1 (Phase 9 added from the competitive analysis)
  COMPETITIVE_ANALYSIS.md     published research, institutional benchmarks, platforms; gaps; additions
  reports/
    P9_ML_Research_Review_and_Handoff.docx / .pdf / .md   the project's main deliverable (27 Sep 2026)
  assets/recommended_architecture.png                     10-layer system diagram from the review
  version-history/            v1.0 charter and roadmap before the amendment
results/
  RESULTS_SUMMARY.md          findings so far (research only) + published reference points
sources/
  chatgpt_conversation_project_option_trading.txt         the source ChatGPT conversation as captured
  chatgpt_handoff_options_research.docx / .md             ChatGPT's own handoff file (self-described as incomplete)
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
- Amendment (2026-10-28): rank by net profit → win rate → trade count; survival/risk limits are
  pass/fail gates; reproduce Bali et al. (2023) and Tan et al. (2024) before building the full
  layered system.

## Open question

From the research doc: keep daily + medium term as the primary horizon, or prioritise intraday?
(No reply recorded; the charter keeps daily + medium term.)

## Disclosure

Research only. Not investment advice. Nothing here shows that any strategy is profitable.
