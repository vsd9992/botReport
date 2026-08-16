# Strategy A and Strategy A+

## Strategy A

### Intent

Strategy A was an early breakout/momentum formulation designed to enter when price, volume, relative strength and volatility aligned.

### Representative tested structure

The documented representative test included:

- 15-minute breakout logic;
- volume confirmation around 1.4x;
- relative-strength filter;
- ATR/volatility controls;
- stop-loss logic around 2% in that test family.

### Observed result

A representative CoinDCX evaluation over a small symbol set produced approximately:

- 13 round trips;
- ~46% win rate;
- average trade P&L ~-0.13%;
- total return ~-0.18%.

### Decision

**Rejected.**

The important conclusion was not merely that this specific parameter set lost money. Strategy A did not provide enough evidence of a robust edge after realistic testing, so the research branch was closed rather than endlessly optimized against the same sample.

---

# Strategy A+

## Why A+ existed

A+ was a stricter, quality-focused evolution of the breakout idea. It attempted to preserve the economically plausible part of the hypothesis while filtering weak market regimes and low-quality breakouts.

## Broad historical evaluation

The surviving A+ configuration was tested over a much broader universe and longer historical period than the initial Strategy A test. Project records from that phase indicate:

- approximately 365 days of research history in the broad run;
- universe screening across roughly 600 symbols before quality/liquidity filtering;
- minimum average 24h volume threshold around INR 25,00,000;
- breakout lookback around 24;
- volume multiplier around 1.2;
- close-position quality filter around 0.55;
- relative strength threshold around 55;
- ATR caps;
- EMA50/two-close style exit behavior in the locked research branch;
- low frequency of approximately 18 trades/year.

These are preserved as project-history values. Exact archived result files from the old machine are not currently stored in this repository, so future recovered artifacts should supersede any approximate historical figure here.

## Why A+ was selected

A+ was not selected because it had the largest backtest return. It was selected because it survived the project’s evidence filters better than all other tested strategy branches.

Relevant reasons:

1. the underlying logic remained interpretable;
2. costs and execution assumptions were included;
3. broader universe/history testing did not immediately destroy the effect;
4. robustness work was strong enough to justify an actual forward test;
5. low frequency was accepted rather than optimized away;
6. competing strategies B, C, D2 and the original A failed to produce comparable evidence.

## Current forward paper implementation

The Linode A+ paper trader currently:

- runs once each hour through `trade-aplus.timer`;
- refreshes the current selected universe;
- evaluates regime first;
- evaluates candidates only under the locked logic;
- tracks simulated cash, positions and trades in SQLite;
- writes the latest report under `/var/lib/trade-lab/reports/aplus/latest.json`;
- starts with INR 5,000 paper capital;
- places no real exchange orders.

### Latest recorded operational status, 2026-08-16

- bootstrap complete: yes;
- selected pairs: 479;
- paper cash: INR 5,000;
- open positions: 0;
- recent completed trades: 0;
- timer: active and running hourly;
- repeated regime rejection: `BTC_4H_BELOW_EMA200`;
- one observed scan had `candidate_count = 1`, but no position was opened because the regime was invalid.

This is expected behavior for a low-frequency strategy. A healthy paper trader is allowed to do nothing.

## Cost configuration correction

The first Linode package contained incorrect cost values that were too high. They were corrected in both A+ and D3 to:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

A+ includes a cost-aware gate, so the earlier incorrect values could have suppressed otherwise valid candidates. No actual paper position had been opened during that period, and the prevailing BTC regime was invalid for the observed scans.

For clean reporting, the official A+ forward paper-test period should be counted from the corrected configuration onward.

## What must happen before A+ is considered live-ready

Paper trading must answer questions historical backtests cannot:

- do the same signals occur in real time as expected?
- are data refreshes reliable across the full universe?
- do entry/exit prices remain realistic?
- do fees/slippage assumptions remain defensible?
- does the strategy preserve expectancy forward?
- is drawdown consistent with historical expectations?
- are there operational failures, API gaps or stale pairs?

The paper stage should not be shortened merely because A+ trades infrequently.