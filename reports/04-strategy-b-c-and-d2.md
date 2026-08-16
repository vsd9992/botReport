# Strategy B, Strategy C and D2

## Strategy B

### Status

**Rejected.**

Strategy B was tested as part of the broader strategy-search program but did not survive the evidence/selection process.

### Evidence limitation

The exact old result artifacts are not currently available in this repository and were produced on the previous development machine. This report therefore preserves the decision without inventing precise statistics.

If old artifacts are recovered, add:

- exact hypothesis and parameter set;
- symbols/date ranges;
- trade count;
- return / drawdown / Sharpe / profit factor;
- robustness results;
- reason for rejection.

## Strategy C

### Status

**Rejected.**

Strategy C likewise failed to produce enough credible edge to warrant promotion.

### Evidence limitation

As with Strategy B, exact archived result files are not currently present here. The known project-level conclusion is that B and C did not outperform the evidence quality achieved by A+ and were discontinued.

## Strategy D family

Strategy D represented a shift from conventional candle-based strategies toward microstructure/short-horizon research.

It evolved through diagnostics rather than being treated as an immediately tradable strategy.

## D2

### Result

D2 is one of the clearest negative results in the project and should remain preserved because it demonstrates why training success alone is insufficient.

Observed decision statistics:

- 12 candidate patterns selected in training;
- 0 selected patterns were net-positive on holdout;
- 0 walk-forward folds produced a net-positive surviving pattern.

### Decision

**Rejected.**

The correct conclusion was not to tune D2 until the holdout became profitable. The failure itself was evidence that the apparent training patterns were not sufficiently robust.

## Lessons carried into D3

D2 directly informed the D3 methodology:

1. use actual recorded order-book and trade-flow data rather than weaker candle proxies;
2. define hypotheses before evaluating results;
3. keep training and holdout separated;
4. require net profitability after costs;
5. continue collecting genuinely unseen forward data;
6. do not approve a strategy based on training results alone.

D3 therefore exists as a stricter diagnostic successor rather than a renamed D2.