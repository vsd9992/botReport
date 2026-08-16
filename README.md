# Trading Bot Research Report Center

This private repository is the durable report center for the crypto trading-bot research project. It records what was tested, what failed, what survived, why decisions were made, what is running now, and what must be verified next.

It is **not** the production bot source repository and must not contain API keys, exchange secrets, credentials, or other sensitive deployment material.

## Current headline status

| Workstream | Status | Current conclusion |
|---|---|---|
| Backtester / simulator | **Locked baseline** | Deterministic event-driven simulator was audited for costs, fills, latency, data quality, aggregation and drawdown accounting. |
| Strategy A | **Rejected** | Original breakout formulation showed no usable edge after realistic testing. |
| Strategy A+ | **Selected / paper trading** | Only strategy so far that survived enough robustness work to justify forward paper trading. Low frequency, approximately 18 trades/year in the broad historical evaluation. |
| Strategy B | **Rejected** | Did not survive the strategy-selection process. Exact old result artifacts are not yet preserved in this report repository. |
| Strategy C | **Rejected** | Did not survive the strategy-selection process. Exact old result artifacts are not yet preserved in this report repository. |
| Strategy D2 | **Rejected** | 12 training patterns selected; 0 were net-positive on holdout and 0 walk-forward folds produced a net-positive pattern. |
| Strategy D3 | **Diagnostic / forward data collection** | Not approved for paper trading. Current Linode implementation tests single-venue CoinDCX microstructure, not cross-exchange lead/lag. |
| TensorTrade | **Deferred** | Investigated, but RL training/framework overhead is a poor fit for the 1 CPU / 1 GB deployment target and is not needed now. |
| ZipLime | **Backlog / independent verifier** | Potential future use to independently reproduce locked A+ results; no migration planned during current paper test. |

## Current deployment

Development, data collection and paper testing are now being done on a small Debian Linode rather than the previous PC workflow.

- 1 CPU
- ~1 GB RAM
- ~495 MB swap currently present
- A+ runs hourly through a systemd timer
- D3 recorder runs continuously
- SQLite is used for the current local data/state stores
- No real-money order placement is enabled in the current research deployment

Latest recorded operational snapshot is documented in [`reports/06-current-deployment.md`](reports/06-current-deployment.md).

## Report index

1. [`reports/00-project-history.md`](reports/00-project-history.md) - project evolution from the original bot through the current research architecture
2. [`reports/01-backtester-audit.md`](reports/01-backtester-audit.md) - simulator design and the D1-D9 audit work
3. [`reports/02-strategy-scoreboard.md`](reports/02-strategy-scoreboard.md) - master strategy result/decision table
4. [`reports/03-strategy-a-and-a-plus.md`](reports/03-strategy-a-and-a-plus.md) - original A failure and A+ selection rationale
5. [`reports/04-strategy-b-c-and-d2.md`](reports/04-strategy-b-c-and-d2.md) - rejected/inconclusive branches
6. [`reports/05-strategy-d3.md`](reports/05-strategy-d3.md) - current microstructure diagnostic and forward-data evidence
7. [`reports/06-current-deployment.md`](reports/06-current-deployment.md) - Linode paper-trading/recorder status and resource observations
8. [`reports/07-next-steps.md`](reports/07-next-steps.md) - ordered next verification work
9. [`reports/08-evidence-register.md`](reports/08-evidence-register.md) - what is verified, approximate, missing, or still awaiting fresh evidence

## Decision discipline

A strategy is not promoted because a chart looks attractive. Promotion requires evidence through progressively harder gates:

1. deterministic strategy definition;
2. realistic costs and execution assumptions;
3. no-lookahead replay;
4. adequate sample/data quality;
5. robustness / parameter sensitivity where applicable;
6. holdout or walk-forward evidence;
7. forward paper/shadow evidence;
8. only then consideration of live capital.

This repository should be updated whenever a strategy is promoted, rejected, materially modified, or produces a new forward-test result.