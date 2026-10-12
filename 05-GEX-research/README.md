# 05 - GEX research: SPY net gamma exposure, 2023 to 2026

The data behind the regime claim in the video: 751 trading days of SPY end-of-day option quotes and open interest from
Theta Data, gamma from an American option engine, rule-based tests of what the sign of GEX at the close says about the
next session, the intraday live book, and a 40-name cross-section.

| Notebook | What it is |
|---|---|
| `gex_history_spy.ipynb` | daily SPY GEX history, next-session tests, episodes, vendor gamma cross-check |
| `gex_intraday_spy.ipynb` | what evaluating the previous close's book at the live spot adds intraday |
| `gex_cross_section.ipynb` | the 40 most active names plus SPX, one year |
| `gex_rows/row01..row10_*.ipynb` | one notebook per demonstrated indication |

Methods, rules, provenance of every number: [`gex_history/README.md`](gex_history/README.md).
The notebooks are committed executed. A rerun needs the `engine_alo` American option engine next to `gex_history/`
(not in this repo) and a Theta Data key. Educational analysis, not a trading strategy.
