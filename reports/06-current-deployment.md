# Current Deployment Status

## Server role

The active development/test environment is now a small Debian Linode. The project no longer assumes access to the old development PC or its historical local files.

Hard operating constraint:

- 1 CPU
- ~1 GB RAM
- limited swap

The server is expected to handle both A+ paper trading and D3 recording/diagnostics in parallel.

## A+ service

### systemd

- `trade-aplus.timer` runs hourly shortly after the hour.
- `trade-aplus.service` refreshes market data and evaluates the locked A+ strategy.

### Latest confirmed state

```text
bootstrap_complete: true
selected_pairs: 479
cash_inr: 5000.0
positions: []
recent_trades: []
```

The service is successfully waking every hour and writing:

`/var/lib/trade-lab/reports/aplus/latest.json`

Recent scans were mostly blocked by:

`BTC_4H_BELOW_EMA200`

One observed scan reached `candidate_count = 1`, but no trade was opened because the regime condition remained invalid.

### Resource observation

Hourly A+ refreshes used roughly:

- ~19-20 seconds CPU time per run;
- ~300 MB peak memory in systemd reporting;
- substantial reported swap high-water values during the refresh.

The current configuration historically used four refresh workers. Because the host has only one CPU, this is an optimization target if resource pressure appears. It is not necessary to change it while the machine remains stable.

### API warnings

Several selected pairs have returned CoinDCX candle endpoint 400/422 errors during hourly refreshes, including examples such as:

- `B-VIC_USDT`
- `B-HFT_USDT`
- `B-PYR_USDT`

These warnings have not stopped the hourly A+ evaluation. They should eventually be handled by pruning unsupported/stale pairs or improving universe refresh logic.

## D3 service

### systemd

- `trade-d3.service` runs continuously.
- `trade-d3-diagnostic.timer` runs scheduled diagnostics.

### Latest confirmed state

```text
book_snapshots: ~73,076
trades: ~382,231
recorded_hours: ~32.02
assets: BTC, ETH, SOL, XRP
database_mb: ~92.31
```

The recorder has remained active and healthy.

## Server memory snapshot

At the captured operational check:

```text
RAM total:      ~967 MiB
RAM used:       ~318 MiB
RAM available:  ~648 MiB
Swap total:     ~495 MiB
Swap used:      ~2.4 MiB
```

The always-on D3 process itself was light enough for the current host. A+ is burstier but short-lived.

## Database sizes at snapshot

- A+ SQLite: ~174 MB
- D3 SQLite: ~92 MB

The A+ database is larger because bootstrap/history storage covers a broad universe.

## Cost configuration

Both A+ and D3 have been manually verified to use the corrected locked values:

```toml
fee_per_side = 0.00025
slippage_per_side = 0.00020
```

No further cost-config change is currently required.

## Current operational judgement

The two workloads can coexist on the 1 CPU / 1 GB Linode at the current stage.

Do not upgrade the server merely because the machine is small. Upgrade only if measured resource pressure begins to affect data collection, hourly A+ execution, or diagnostic reliability.

Priority if pressure develops:

1. reduce A+ refresh concurrency;
2. bound/chunk D3 diagnostics;
3. reduce unnecessary retained data/logging;
4. only then consider a larger VM.