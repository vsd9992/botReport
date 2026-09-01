# Strategy Scoreboard

This is the master decision table. It should be updated whenever a strategy changes state.

## Status definitions

- **Selected**: enough evidence to justify forward paper trading.
- **Diagnostic**: research continues, but no strategy approval.
- **Rejected**: failed evidence/robustness tests or showed no usable edge.
- **Deferred**: technically interesting, but not justified for current resources/priorities.

## Master scoreboard

| Strategy / branch | Core idea | Evidence state | Decision | Why |
|---|---|---|---|---|
| Strategy A | Breakout + volume + RS + ATR + stop | Historical test negative | **Rejected** | Representative test produced ~13 round trips, ~46% win rate, ~-0.13% avg trade P&L, ~-0.18% total return. No robust edge demonstrated. |
| Strategy A+ | Refined quality breakout / regime-filtered momentum | Broader historical robustness work survived; forward paper test active | **Selected** | Only strategy so far that produced enough credible evidence to justify forward paper trading. Low frequency, ~18 trades/year in broad historical evaluation. No paper positions have opened yet in the observed forward period because the regime filters have remained restrictive. |
| Strategy B | Alternative strategy family | Failed selection process | **Rejected** | Did not survive testing. Exact legacy artifacts are not yet preserved here. |
| Strategy C | Alternative strategy family | Failed selection process | **Rejected** | Did not survive testing. Exact legacy artifacts are not yet preserved here. |
| Strategy D / early D work | Microstructure/short-horizon research family | Exploratory | **Superseded by D2/D3 diagnostics** | Research branch rather than validated strategy. |
| Strategy D2 | Candidate microstructure patterns | Training patterns failed holdout / walk-forward | **Rejected** | 12 patterns selected in training; 0 net-positive on holdout; 0 walk-forward folds produced a net-positive pattern. |
| Strategy D3 | CoinDCX order-book/trade-flow microstructure | Rolling forward diagnostic reached 1 eligible training pattern and 1 holdout-tested pattern, but 0 net-positive holdout patterns as of 2026-09-01 | **Diagnostic, current formulation failing promotion gate** | A training pattern can now qualify, but it fails net profitability on holdout. Do not promote to paper trading. Confirm a clean full 168-hour window, then close the current hypothesis set or replace it only with a separately predeclared D3.x design. |
| TensorTrade RL branch | Reinforcement-learning framework | Feasibility review only | **Deferred** | Heavy training stack adds complexity and resource pressure; 1 CPU / 1 GB Linode is the hard operating constraint. No need while deterministic research remains unresolved. |
| ZipLime verifier | Independent Python backtest implementation | Not started | **Backlog** | Potentially valuable to independently reproduce locked A+ without changing A+ itself. |

## Current ranking

1. **A+** - only retained tradable candidate; currently in forward paper trading.
2. **D3** - current formulation has failed its latest holdout net-profitability gate; keep only as diagnostic/data infrastructure until the full-window check is completed.
3. Everything else tested so far - rejected, superseded, deferred, or not yet started.

## Promotion logic

A strategy does not advance merely because its training P&L is positive.

Minimum promotion sequence:

1. hypothesis defined before evaluation;
2. deterministic implementation;
3. realistic fee/slippage/spread assumptions;
4. valid data and sufficient sample;
5. robustness / sensitivity testing;
6. holdout and/or walk-forward evidence;
7. forward paper/shadow evidence;
8. only then live-capital consideration.

## Why A+ won

A+ was selected because it was the only branch that survived enough of this process to justify forward testing. Its low trade frequency was accepted rather than 'fixed' through looser filters. Increasing trade count by weakening the strategy would simply manufacture more opportunities to be wrong.

The absence of paper trades so far is not itself evidence against A+. A low-frequency strategy with explicit market-regime filters is expected to spend substantial time inactive. Its paper evaluation begins when valid signals occur; inactivity should not be converted into artificial trades by relaxing the locked rules.

## Why D3 has not been promoted

D3 asks whether a measurable short-horizon microstructure effect exists in genuinely unseen CoinDCX data and whether that effect is economically large enough after costs.

The latest rolling diagnostic is more informative than the early 32-hour snapshot: one pattern now passes training eligibility and reaches holdout, but it is not net positive there. That is a validation failure, not a near-pass. Unless a data-quality problem explains it, the current hypothesis set should not be tuned repeatedly against the same accumulated sample.