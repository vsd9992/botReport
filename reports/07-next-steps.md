# Next Steps and Verification Plan

This is the ordered work queue. It should remain conservative. The project is now in forward-validation mode, so unnecessary parameter changes are more harmful than helpful.

## Priority 1: Let A+ paper trading run unchanged

Current objective:

> Verify that the historically selected A+ behaves operationally and economically as expected in genuinely unseen live market conditions.

Do not loosen entry rules merely because the strategy is quiet.

Track over time:

- number of hourly scans;
- valid/invalid regime frequency;
- candidate count;
- opened positions;
- skipped positions and reason;
- entry/exit timestamps;
- simulated fill prices;
- fees/slippage;
- gross and net P&L;
- max drawdown;
- duration by trade;
- API/data errors;
- stale/unavailable symbols.

A+ is low frequency. The paper stage therefore needs enough calendar time and trades to be meaningful.

## Priority 2: Complete the first clean 7-day D3 window

Do not change D3 thresholds or hypotheses during the first clean forward collection period.

At 7 days, rerun diagnostics with the corrected cost configuration and capture the resulting files in this repository or a summarized report.

Required review:

- data quality by asset;
- actual recorded duration;
- eligible training patterns;
- selected patterns;
- holdout results;
- net-positive holdout patterns;
- day-by-day consistency;
- horizon consistency;
- spread/cost sensitivity;
- signal-strength monotonicity.

Possible outcomes:

### A. No eligible pattern

Reject or redesign D3 only after writing a new predeclared hypothesis. Do not tune retrospectively until something passes.

### B. Training-positive but holdout-negative

Reject the pattern. This is essentially the D2 lesson repeating itself.

### C. Holdout-positive gross but not net

Record the predictive effect as scientifically interesting but economically untradable under taker execution.

### D. Holdout-positive net

Continue forward/shadow validation. Do not immediately place real orders.

## Priority 3: Review A+ universe/API hygiene

Current hourly logs show repeated 400/422 candle errors for a small number of pairs.

After the forward process is stable, improve universe maintenance so delisted/unsupported/stale pairs are excluded without changing the strategy logic.

This is an operational-data fix, not a strategy optimization.

## Priority 4: Resource optimization only if measured pressure appears

The current 1 CPU / 1 GB Linode is functioning.

If A+ refreshes begin interfering with D3 recording:

1. reduce A+ `refresh_workers` from 4 to 2;
2. test whether 1 worker is still fast enough;
3. chunk/bound D3 diagnostics;
4. monitor database and journal growth.

Do not migrate to a larger server preemptively.

## Priority 5: Independent A+ verification

After the current paper infrastructure is stable, consider implementing the **locked A+ specification** in a second independent framework such as ZipLime.

Purpose:

- reproduce the same historical strategy independently;
- compare trade timestamps and results;
- detect hidden assumptions or implementation-specific behavior.

Rules:

- no parameter optimization;
- no AI-generated strategy changes;
- same costs;
- same strategy definition;
- same comparable data window where possible.

A disagreement between engines is a diagnostic finding, not an invitation to choose whichever result is prettier.

## Priority 6: Consider live A+ only after sufficient paper evidence

Before real capital, verify:

- enough forward trades to compare with historical expectancy;
- stable operation across server restarts/API issues;
- exact live fee tier;
- exchange order constraints/minimum sizes;
- realistic slippage/fill assumptions;
- hard risk limits;
- kill switch;
- duplicate-order protection;
- persistence/recovery after crash;
- reconciliation against exchange state.

No live capital is currently authorized by the research status.

## Deferred work

The following should stay out of the critical path for now:

- TensorTrade / reinforcement learning;
- heavy ML models;
- strategy-generation AI;
- broad parameter searches;
- changing A+ simply to increase trade frequency;
- forcing D3 into paper trading before it survives validation.

The highest-value action now is painfully ordinary: collect clean forward evidence without moving the goalposts.