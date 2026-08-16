# Backtester and Simulator Audit

## Why the audit existed

The research process deliberately treated the simulator as a source of risk. A profitable strategy result is meaningless if the simulator grants fills that could not happen, looks into the future, ignores spread/costs, or reports risk incorrectly.

The backtester was therefore audited through a D1-D9 diagnostic program before strategy results were trusted.

## Locked simulator principles

The baseline event-driven simulator was designed around:

- deterministic replay;
- exact event timestamps;
- signal calculation before fill;
- no lookahead;
- default one-event execution latency;
- quantity-aware fills;
- explicit fees;
- explicit slippage;
- spread gate / bid-ask awareness where appropriate;
- mark-to-market equity and drawdown;
- reproducible output.

It supported recorded CoinDCX/CoinSwitch market streams and fixed/native timestamp buckets during the earlier research phase.

## D1-D9 audit summary

| Audit | Area | Outcome |
|---|---|---|
| D1 | Cost model | Fees/slippage explicitly modeled instead of assuming frictionless execution. |
| D2 | Bid/ask and fill semantics | Execution semantics tightened so signals could not receive unrealistic mid-price-style fills. |
| D3 | Microstructure / pre-trade diagnostics | Added deeper diagnostics using order-book/trade-flow information. This later developed into the current D3 research branch. |
| D4 | Partial fills | Quantity/fill handling audited so simulated orders were not automatically assumed fully fillable. |
| D5 | Latency sensitivity | One-event latency locked as default; sensitivity work used to expose strategies dependent on instant fills. |
| D6 | Data validation | Data-quality validation added so corrupted/gapped streams could not silently drive results. |
| D7 | Event aggregation | Timestamp/event aggregation semantics audited for deterministic behavior. |
| D8 | Drawdown | Drawdown corrected to mark-to-market equity rather than only realized/closed positions. |
| D9 | Strategy A edge check | Original Strategy A failed the evidence test and was closed as a no-edge formulation. |

## Baseline trading-cost assumptions

The research configuration ultimately locked the following relevant CoinDCX assumptions for the current paper/diagnostic deployment:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

That corresponds to:

- fee assumption: 0.025% per side;
- slippage assumption: 0.02% per side;
- approximately 9 bps round-trip from fee + slippage before any separately represented spread effect.

These values are research assumptions and should be re-verified against the actual exchange fee tier before live deployment.

## Why this matters for D3

D3 studies effects that can be measured in only a few basis points. A tiny microstructure prediction can be statistically detectable yet economically useless after fees, slippage and spread.

Therefore D3 must be evaluated on **net**, executable economics. A positive gross directional effect is not enough.

## Why this matters for A+

A+ includes a cost-aware opportunity filter. Incorrectly high fee/slippage settings can reject valid candidates even before a paper trade is opened.

This became operationally relevant when the first Linode package was discovered to contain overly high cost values. The configuration was corrected before the official forward-paper record was accepted.

## Current audit status

The simulator/audit layer is considered the locked baseline for ongoing research. Changes to execution semantics should not be made casually while a strategy is undergoing forward testing because doing so would move the goalposts and invalidate comparison with historical evidence.