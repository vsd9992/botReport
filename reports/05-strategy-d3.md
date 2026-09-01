# Strategy D3: CoinDCX Microstructure Diagnostic

## Status

**Rejected for trading in its current formulation. Recorder/data retained only as research infrastructure.**

The decisive forward result is now complete enough to close this hypothesis set:

- total recorded duration: **405.54 hours**;
- assets: BTC, ETH, SOL, XRP;
- all four assets usable in the latest diagnostic;
- `eligible_training_patterns`: **1**;
- `selected_patterns_with_holdout`: **1**;
- `holdout_net_positive_patterns`: **0**.

The current D3 formulation therefore fails its own holdout net-profitability promotion gate after substantially more than the required 168-hour window.

## Current assumption lock

The Linode implementation explicitly states:

> Single-venue CoinDCX microstructure only. No cross-exchange lead/lag assumption.

Earlier project discussion included CoinDCX/CoinSwitch lead-lag concepts, but this deployed D3 experiment was a single-venue CoinDCX order-book/trade-flow test.

## Assets and forward dataset

Latest server status on 2026-09-01:

```text
recorded_hours: 405.54
database_mb: 3532.5
book_snapshots: 1,029,902
trades: 16,241,304
assets: BTC, ETH, SOL, XRP
```

The 405.54-hour recorded span is comfortably larger than the 168-hour diagnostic window. The latest diagnostic reported all four assets usable.

The database has grown to roughly **3.5 GB**, so continuing to record without a declared follow-on hypothesis would consume storage for no immediate validation benefit.

## Final rolling diagnostic result for current hypothesis set

```json
{
  "status": "COMPLETE",
  "diagnostic_hours": 168,
  "assets": ["BTC", "ETH", "SOL", "XRP"],
  "assets_usable": 4,
  "eligible_training_patterns": 1,
  "selected_patterns_with_holdout": 1,
  "holdout_net_positive_patterns": 0,
  "assumption_lock": "Single-venue CoinDCX microstructure only. No cross-exchange lead/lag assumption.",
  "interpretation_lock": "Diagnostic only. No live order placement and no strategy approval from training results alone."
}
```

Interpretation:

1. At least one pattern became strong enough to satisfy the training eligibility rules.
2. The pattern was then evaluated on holdout data.
3. It did not remain net profitable after the locked execution-cost assumptions.
4. The failure occurs at exactly the gate intended to distinguish an in-sample effect from a strategy candidate.
5. The correct decision is rejection, not threshold relaxation.

## Earlier observations

The first ~32-hour diagnostic showed no eligible training patterns. At that stage the data was too young to make a final decision.

Early inspection also showed some positive gross BTC buy-side-imbalance behavior at several horizons, including sub-1-bp average gross effects. Those observations were scientifically interesting but economically too small relative to realistic taker costs.

Representative early values were approximately:

| Sample | Horizon | Gross mean | Gross PF | Win rate |
|---|---:|---:|---:|---:|
| Train | 180s | +0.481 bps | 1.95 | 51.0% |
| Train | 300s | +0.463 bps | 1.62 | 54.9% |
| Holdout | 180s | +0.471 bps | 2.62 | 53.0% |
| Holdout | 300s | +0.551 bps | 2.43 | 58.3% |
| Holdout | 900s | +0.895 bps | 2.90 | 63.4% |

With the locked cost assumptions, fee + slippage alone are roughly 9 bps round trip before additional spread effects. A sub-1-bp directional effect is therefore not enough for a taker-style strategy.

## Cost configuration incident

The initial Linode package accidentally used:

```toml
fee_per_side = 0.0060
slippage_per_side = 0.0010
```

This was corrected and both A+ and D3 were subsequently verified to use:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

The raw D3 market data was unaffected by the earlier configuration error.

## Final decision

**D3 current formulation is rejected. Do not paper trade it.**

Recommended operational action:

1. stop the continuous D3 recorder and scheduled D3 diagnostic now that the experiment is complete;
2. retain the existing SQLite dataset and reports unchanged as evidence/research material;
3. do not tune the current D3 thresholds against this accumulated dataset;
4. only restart collection/testing when a genuinely new D3.x hypothesis has been specified before evaluation;
5. if a future D3.x experiment uses the retained dataset, create a new untouched forward holdout period for final validation.

The recorder itself proved useful and operationally feasible. The strategy hypothesis did not.