# Strategy D3: CoinDCX Microstructure Diagnostic

## Status

**Diagnostic / forward research only. Not approved for paper trading.**

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

## Current forward snapshot

Latest recorded status from 2026-08-16:

- recorder runtime: ~32 hours;
- book snapshots: ~73,076;
- trades: ~382,231;
- D3 database: ~92 MB;
- assets usable: 4;
- `eligible_training_patterns`: 0;
- `selected_patterns_with_holdout`: 0;
- `holdout_net_positive_patterns`: 0.

The diagnostic command reports `diagnostic_hours: 168`, but this is the configured maximum analysis window, not proof that 168 hours have already been recorded.

## Current signal observations

Early diagnostic inspection showed that BTC buy-side imbalance had some positive **gross** directional behavior at several horizons. Representative early values observed before the full 7-day sample included approximately:

| Sample | Horizon | Gross mean | Gross PF | Win rate |
|---|---:|---:|---:|---:|
| Train | 180s | +0.481 bps | 1.95 | 51.0% |
| Train | 300s | +0.463 bps | 1.62 | 54.9% |
| Holdout | 180s | +0.471 bps | 2.62 | 53.0% |
| Holdout | 300s | +0.551 bps | 2.43 | 58.3% |
| Holdout | 900s | +0.895 bps | 2.90 | 63.4% |

These are **not strategy approval results**. They illustrate an important distinction: a statistically interesting gross directional effect can still be economically untradable.

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

The active configuration has since been corrected to:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

The raw recorded D3 market data was unaffected and remains valid. Diagnostics must simply be rerun with the corrected costs.

## Resource observations

At the ~32-hour snapshot:

- continuous D3 process CPU use was around 0.4%;
- recorder RSS was under ~100 MB in the `ps` snapshot;
- systemd memory accounting showed the service around 150 MB high-water/current control values;
- the 1 GB Linode still had substantial available memory.

The continuous recorder therefore appears feasible on the current server.

Longer-term diagnostics require care because loading an ever-growing SQLite dataset into large in-memory pandas DataFrames would eventually become inappropriate for a 1 GB machine. Bounded/chunked analysis should be preferred as the dataset grows.

## Current decision

Do **not** paper trade D3 yet.

Continue recording untouched forward data until at least the first full 7-day window is complete. Then rerun the diagnostic under the corrected cost model and evaluate:

- eligible training patterns;
- holdout survival;
- net expectancy;
- profit factor;
- win rate;
- stability by day;
- stability by asset;
- stability by horizon;
- score/strength monotonicity;
- whether any effect is large enough to overcome realistic costs.

If no pattern survives, D3 should be rejected or redesigned based on a new predeclared hypothesis, not retrospectively tuned against this dataset.