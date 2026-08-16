# Evidence Register

This file separates hard evidence from remembered/approximate project history. It exists to stop the report center from gradually turning remembered numbers into fake precision.

## Evidence classes

- **Verified-current**: directly observed in current logs/config/results.
- **Verified-historical**: supported by preserved historical project results.
- **Approximate-history**: remembered project value, useful for context but should be superseded if the old artifact is recovered.
- **Missing-artifact**: conclusion known, exact underlying result file not currently available.
- **In-progress**: current forward work has not yet reached a decision point.

## Register

| Item | Value / conclusion | Class | Notes |
|---|---|---|---|
| Strategy A representative result | ~13 round trips, ~46% win, ~-0.13% avg trade, ~-0.18% total return | Verified-historical | Used to close original A as no-edge formulation. |
| A+ broad frequency | ~18 trades/year | Approximate-history | Broad historical evaluation; retain until exact old result artifact is recovered. |
| A+ broad universe | roughly 600 symbols screened | Approximate-history | Historical research context. |
| A+ historical window | roughly 365 days | Approximate-history | Historical research context. |
| B decision | rejected | Missing-artifact | Exact old result files not currently in this repository. |
| C decision | rejected | Missing-artifact | Exact old result files not currently in this repository. |
| D2 selected training patterns | 12 | Verified-historical | None survived economically on holdout. |
| D2 holdout net-positive | 0 | Verified-historical | Strong rejection evidence. |
| D2 walk-forward net-positive folds | 0 | Verified-historical | Strong rejection evidence. |
| Current A+ selected pairs | 479 | Verified-current | Linode status output. |
| Current A+ paper capital | INR 5,000 | Verified-current | Linode status/latest report. |
| Current A+ open positions | 0 | Verified-current | Latest snapshot 2026-08-16. |
| Current A+ completed trades | 0 | Verified-current | Latest snapshot 2026-08-16. |
| Current A+ common regime block | BTC_4H_BELOW_EMA200 | Verified-current | Repeated hourly logs. |
| Current D3 assets | BTC, ETH, SOL, XRP | Verified-current | All four usable. |
| Current D3 recorded duration | ~32.02 hours | Verified-current | Latest status output. |
| Current D3 book snapshots | ~73,076 | Verified-current | Latest status output. |
| Current D3 trades | ~382,231 | Verified-current | Latest status output. |
| Current D3 eligible training patterns | 0 | Verified-current | Diagnostic output. |
| Current D3 selected patterns with holdout | 0 | Verified-current | Diagnostic output. |
| Current D3 holdout net-positive patterns | 0 | Verified-current | Diagnostic output. |
| D3 assumption | single-venue CoinDCX only | Verified-current | Explicit diagnostic lock. |
| Cost assumption | fee 0.00025/side, slippage 0.00020/side | Verified-current | User verified both A+ and D3 config after correction. |
| D3 first 7-day forward decision | pending | In-progress | Do not conclude before clean window is complete. |
| A+ forward expectancy | pending | In-progress | Low-frequency paper test only just started. |

## Known incident: initial Linode cost settings

The initial deployment package contained excessively high cost settings:

```text
fee_per_side = 0.0060
slippage_per_side = 0.0010
```

These were corrected to the locked assumptions. Raw D3 market data remains usable because the mistake affected analysis/economic assumptions rather than the recorded public market events.

## Update rule

Whenever an old artifact is recovered, replace an `Approximate-history` or `Missing-artifact` entry with the exact evidence and note the source/result filename.

Whenever a forward run reaches a decision point, preserve the decision and the underlying summary before starting a modified hypothesis. Otherwise yesterday's failed experiment has an unfortunate tendency to become tomorrow's vague success story.