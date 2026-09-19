# World Cup Oracle: Original Baseline

An experimental football simulation project using team-strength heuristics
and Monte Carlo tournament replays. This repository is the original baseline,
not the location of the latest evaluation or referee changes.

## Verified baseline result

The 2022 replay at **50,000 simulations, seed 42** produces **40/64 BPS**,
below the historical 45/64 threshold. It selects France rather than Argentina.
The stage points are 12 R16 + 12 QF + 6 SF + 10 finalist + 0 champion.
This is an in-sample historical replay, not evidence of held-out accuracy.

```bash
git clone https://github.com/fatehaszaman/world-cup-oracle.git
cd world-cup-oracle
python -m pip install -r requirements.txt
python -m backtest.wc2022_backtest
```

## Acknowledging the testing and documentation mistake

I previously made cross-repository consistency claims before checking the
committed code. This repository's 40/64 baseline was reproducible, but the
v2 and trials PASS claims were not supported. The original README also
included unverified sample forecasts and a static tests-passing badge;
those are removed rather than represented as current results.

The first correction incorrectly described the trials form-boost table as
pre-existing code that merely needed connecting. It was added during that
correction and is outcome-informed. Its higher replay score must not be
presented as independent validation.

The full disclosure and reproducible comparison are in the
[trials audit](https://github.com/fatehaszaman/world-cup-oracle-trials/blob/main/AUDIT.md).
Previous versions are retained in Git history.

## Repository roles

- **This baseline:** 2022 BPS 40/64 at the settings above.
- **[v2](https://github.com/fatehaszaman/world-cup-oracle-v2):** 2022 BPS 40/64
  and 2018 BPS 25/64 at its documented settings. Its dimension-weight proposal
  is not connected to the fixed-score 2022 backtest.
- **[Trials](https://github.com/fatehaszaman/world-cup-oracle-trials):**
  maintained experiments with bracket/market/xG diagnostics, correction
  history, and optional 2026 match-level referee dampening.

## Test and data limitations

The full suite is not green: the 2026-09-19 follow-up on Python 3.14 recorded
34 passes and 8 failures with `python -m pytest -q`. There are no collection
errors or skipped tests. Imports, compilation, and all three script smoke
checks pass. Remaining failures concern missing Qatar scorer inputs,
Brazil's historical rank, and four unchanged probability hypotheses.
See the [full audit and logs](https://github.com/fatehaszaman/world-cup-oracle-trials/blob/main/AUDIT.md).

The generic engine's knockout completion, round labels, referee draw-mass
handling, and examples have been repaired. `examples/run_prediction.py`
now computes output rather than printing fabricated sample probabilities.
It remains a synthetic 48-team scenario with only 24 knockout entrants and
byes, not an official 2026 bracket. The separate historical replay above is
unchanged by these generic-engine repairs.
No claim of production readiness, current squad data, validated causal
referee effects, or guaranteed forecast accuracy is made.

Maintained by [fatehaszaman](https://github.com/fatehaszaman).
