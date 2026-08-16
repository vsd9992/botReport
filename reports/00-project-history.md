# Project History

## Purpose

This document records the evolution of the trading-bot project from the original implementation through the current A+ paper-trading and D3 forward-diagnostic stage.

The goal is not to preserve every discarded line of code. The goal is to preserve the reasoning trail: what was built, what was tested, what failed, what survived, and why.

## 1. Original bot and first review

The project began with an existing crypto-trading bot implementation that needed to be evaluated rather than trusted at face value.

The early review exposed the central problem that shaped the rest of the project: a backtest can look convincing while still being wrong because of execution assumptions, timing, data handling, or overfitting.

The project therefore shifted away from judging the original bot by headline profit and toward building a repeatable research pipeline.

## 2. Backtester rebuilt around deterministic replay

A deterministic event-driven backtester became the baseline research tool.

Core principles locked during this phase:

- exact timestamp/event replay;
- signal generation before fill;
- no lookahead;
- one-event latency as the default execution assumption;
- quantity-aware fills;
- realistic trading costs;
- spread/slippage controls;
- mark-to-market drawdown rather than only closed-trade drawdown;
- reproducible output and diagnostics.

The simulator was then subjected to a structured D1-D9 audit. See `01-backtester-audit.md`.

## 3. Strategy A tested and rejected

The original Strategy A family was a breakout-style approach using momentum/relative-strength/volatility filters.

A representative evaluated configuration used:

- 15-minute breakout logic;
- volume confirmation;
- relative strength;
- ATR filtering;
- stop-loss logic.

In the documented evaluation on CoinDCX across a small symbol set, the strategy produced approximately:

- 13 round trips;
- ~46% win rate;
- average trade P&L around -0.13%;
- total return around -0.18%.

The conclusion was not that one parameter needed another adjustment. The conclusion was that the tested formulation did not demonstrate a robust edge.

Strategy A was therefore rejected.

## 4. Strategy A+ developed and selected

Rather than abandon the underlying momentum/breakout idea entirely, the strategy was refined into Strategy A+ and subjected to broader testing.

The broader research used a much larger universe and longer historical period than the original Strategy A test. The surviving A+ configuration became the only strategy so far that justified promotion beyond historical research.

Important characteristics:

- low frequency;
- roughly 18 trades/year in the broad historical evaluation;
- more selective regime and quality filters;
- realistic cost and execution assumptions retained;
- chosen because it survived the evidence process better than competing strategies, not because it traded often.

A+ was promoted to forward paper trading.

## 5. Strategy B and C branches

Additional strategy families B and C were investigated during the research program.

Neither produced enough credible evidence to survive the selection process.

They are retained in the report history as rejected branches. Exact old run artifacts are not currently available in this repository, so this report deliberately does not invent precision that is no longer available.

If old artifacts are recovered later, their exact parameters/results should be added to the evidence register.

## 6. Strategy D research family

Strategy D shifted the research focus toward shorter-horizon market microstructure.

The D family evolved through multiple diagnostic stages rather than immediately being treated as a tradable strategy.

### D2

D2 produced candidate patterns in training, but failed the harder validation tests:

- 12 patterns selected in training;
- 0 net-positive patterns on holdout;
- 0 walk-forward folds with a net-positive surviving pattern.

D2 was rejected.

### D3

D3 moved to recorded order-book/trade-flow data and explicit microstructure hypotheses.

The current Linode D3 implementation is specifically:

> Single-venue CoinDCX microstructure only. No cross-exchange lead/lag assumption.

This distinction matters because earlier project discussion also considered cross-exchange CoinDCX/CoinSwitch lead-lag effects. The current D3 forward recorder is not that experiment.

D3 is still diagnostic research, not an approved strategy.

## 7. Forward infrastructure moved to Linode

The user decided to stop relying on the old PC for development and testing and move the active workflow to a small Linode.

Current design constraint:

- Debian server;
- 1 CPU;
- ~1 GB RAM;
- limited swap;
- development, recording and paper testing should all fit this machine.

This resource constraint caused the project to favor simple, inspectable systems instead of heavy ML/RL infrastructure.

TensorTrade was investigated and then deferred.

ZipLime was later identified as potentially useful for an independent future reproduction of A+, but it is not part of the current running system.

## 8. Current forward stage

As of 2026-08-16:

### A+

- bootstrap complete;
- 479 selected pairs in the current Linode dataset;
- hourly evaluation timer running;
- starting paper capital: INR 5,000;
- no open positions yet;
- no completed paper trades yet;
- many recent hourly scans correctly blocked by the BTC 4H below EMA200 regime condition;
- one observed scan produced one candidate, but the regime was invalid, so no trade was opened.

### D3

- continuous recorder active;
- BTC, ETH, SOL and XRP usable;
- approximately 32 hours of forward data at the latest recorded snapshot;
- ~73k order-book snapshots;
- ~382k trades;
- D3 diagnostic status complete for the available window;
- 0 eligible training patterns under the current promotion thresholds;
- 0 selected patterns with holdout;
- 0 holdout net-positive patterns.

The recorder is healthy and the research continues. There is no justification yet for D3 paper trading.

## 9. Cost-configuration correction

The first Linode package accidentally contained unrealistic per-side cost values:

- `fee_per_side = 0.0060`
- `slippage_per_side = 0.0010`

These were much higher than the locked research assumptions and distorted both D3 economics and A+ entry gating.

The configuration was corrected to:

- `fee_per_side = 0.00025`
- `slippage_per_side = 0.00020`

The correction applies to both A+ and D3.

Important interpretation:

- raw D3 recorded market data from before the fix remains valid;
- D3 diagnostics must use the corrected cost model;
- early A+ operational scans remain useful for system validation, but the official forward paper-test record should be counted from the corrected configuration onward.

## 10. Current research philosophy

The project now follows a deliberately conservative rule:

> Fewer strategies with evidence are better than many strategies with attractive backtests.

At the current stage, the scoreboard is simple:

- A+ is the selected strategy and is in paper trading;
- D3 is still a research diagnostic;
- B, C and D2 are rejected;
- nothing else has earned promotion.