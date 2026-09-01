# Strategy D3: CoinDCX Microstructure Diagnostic

## Status

**Diagnostic / forward research only. Not approved for paper trading.**

As of the 2026-09-01 rolling diagnostic, the current D3 formulation has **failed the holdout net-profitability promotion gate**: one training pattern became eligible and was evaluated on holdout, but zero holdout patterns were net positive.

This does not justify loosening thresholds or tuning the same sample retrospectively. The current hypothesis set should either be closed after confirming the recorder has supplied a full clean 168-hour window, or replaced by a separately specified D3.x hypothesis before testing.

## Current assumption lock

The Linode implementation explicitly states:

> Single-venue CoinDCX microstructure only. No cross-exchange lead/lag assumption.

This is important. Earlier project discussion included cross-exchange CoinDCX/CoinSwitch lead-lag concepts, but the currently running D3 recorder/evaluator is a single-venue experiment.

## Assets

Current live recorder tracks:

- BTC
- ETH
- SOL
- XRP

All four were usable in the latest diagnostic snapshot.

## Data flow

The recorder continuously captures public CoinDCX order-book/trade information and stores it in SQLite. The diagnostic then evaluates predefined microstructure patterns over bounded horizons.

The current D3 framework uses forward horizons including:

- 30 seconds
- 60 seconds
- 180 seconds
- 300 seconds
- 900 seconds

Execution economics are evaluated against realistic trading costs rather than gross price movement alone.

## Initial forward snapshot: 2026-08-16

- recorder runtime: ~32 hours;
- book snapshots: ~73,076;
- trades: ~382,231;
- D3 database: ~92 MB;
- assets usable: 4;
- `eligible_training_patterns`: 0;
- `selected_patterns_with_holdout`: 0;
- `holdout_net_positive_patterns`: 0.

The diagnostic command reports `diagnostic_hours: 168`, but this is the configured maximum analysis window, not by itself proof that 168 hours were recorded.

## Rolling diagnostic snapshot: 2026-09-01

Latest reported result:

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

1. D3 now finds at least one training pattern that satisfies the training eligibility rules.
2. That pattern was eligible for holdout evaluation.
3. It did **not** remain net positive on holdout.
4. Therefore the current D3 formulation still has no strategy that passes its own promotion rules.

This is a more informative failure than the first ~32-hour snapshot: the issue is no longer merely that nothing qualifies in training. A pattern can qualify in-sample and then fail out-of-sample economics, which is exactly the behavior the holdout gate is designed to catch.

## Early signal observations

Early diagnostic inspection showed that BTC buy-side imbalance had some positive **gross** directional behavior at several horizons. Representative early values observed before the full 7-day sample included approximately:

| Sample | Horizon | Gross mean | Gross PF | Win rate |
|---|---:|---:|---:|---:|
| Train | 180s | +0.481 bps | 1.95 | 51.0% |
| Train | 300s | +0.463 bps | 1.62 | 54.9% |
| Holdout | 180s | +0.471 bps | 2.62 | 53.0% |
| Holdout | 300s | +0.551 bps | 2.43 | 58.3% |
| Holdout | 900s | +0.895 bps | 2.90 | 63.4% |

These were **not strategy approval results**. They illustrated an important distinction: a statistically interesting gross directional effect can still be economically untradable.

With the locked cost assumptions, fee + slippage alone are roughly 9 bps round trip before any additional spread effect. A gross edge below 1 bp is therefore not enough for a taker-style strategy.

## Spread observations

Early data-quality reports showed materially different spread regimes by asset. Approximate mean spread observations from the first ~32 hours were:

- BTC: ~0.016 bps
- ETH: ~0.288 bps
- SOL: ~4.364 bps
- XRP: ~4.865 bps

This makes SOL/XRP especially difficult for very small microstructure effects to monetize using taker execution.

## Cost configuration incident

The initial Linode package accidentally used:

```toml
fee_per_side = 0.0060
slippage_per_side = 0.0010
```

This created absurdly punitive net results and was not the locked research cost model.

The active configuration was subsequently verified as:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

The raw recorded D3 market data was unaffected by the earlier error.

## Resource observations

At the ~32-hour snapshot:

- continuous D3 process CPU use was around 0.4%;
- recorder RSS was under ~100 MB in the `ps` snapshot;
- systemd memory accounting showed the service around 150 MB high-water/current control values;
- the 1 GB Linode still had substantial available memory.

The continuous recorder therefore appears feasible on the current server.

Longer-term diagnostics require care because loading an ever-growing SQLite dataset into large in-memory pandas DataFrames would eventually become inappropriate for a 1 GB machine. Bounded/chunked analysis should be preferred as the dataset grows.

## Current decision

Do **not** paper trade D3.

The latest rolling diagnostic has one training-selected pattern and zero net-positive holdout patterns. The current strategy formulation therefore fails its validation gate.

Recommended handling:

1. confirm `recorded_hours` is comfortably above 168 and the relevant 168-hour window has no material data gaps;
2. preserve the current report/result without parameter changes;
3. treat the present D3 hypothesis set as failed if the full-window/data-quality check is clean;
4. keep the lightweight recorder running if desired because the raw microstructure dataset remains useful infrastructure;
5. only test a D3.x successor after writing a new hypothesis and thresholds before looking at its result.

Do not repeatedly retune the existing D3 rules against the accumulated dataset until something turns green. That would convert the holdout set into training data and destroy the value of the experiment.