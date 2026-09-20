# Algorithm guide

These cards describe the original baseline's generic simulator, not an official
tournament format or a validated forecast. Numerical costs assume fixed-width
arithmetic and bounded distribution parameters.

## Simulate a match

Implementation: [`TournamentSimulator.simulate_match`](../oracle/monte_carlo.py).

```text
# Match Monte Carlo / Poisson Outcome Counts
# Input: two team identifiers, score map, P simulated matches
# Output: win/draw probabilities, mean goals, and adjustment metadata
# Time: expected O(P), excluding optional referee lookup/adjustment
# Memory: O(P) temporary goal/comparison arrays; O(1) output

LOOK UP scores, using 0.50 for missing teams
CLIP strength difference and calculate floored goal-rate parameters
DRAW P Poisson goal counts for each team
COUNT A wins, B wins, and draws
DIVIDE counts by P
IF referee adjustment is available:
    ADJUST conditional decisive probabilities while preserving draw mass
RETURN rounded probabilities and sample goal means
```

Use P > 0. Random sampling cost is expected, not a strict bound for every
internal generator operation. Missing scores use a fallback; they do not mean
observed average strength. Referee failures can be caught and skipped.

## Aggregate repeated tournaments

Implementation: [`TournamentSimulator.run_tournament`](../oracle/monte_carlo.py).

```text
# Tournament Summary / Materialize Reach Indicators
# Input: R runs, T unique teams, K=6 recorded stages, scores and groups
# Output: team probability table sorted by champion probability
# Time: O(R*(C_run + T*K) + T*log T)
# Memory: O(R*T*K + T*K) plus simulator and one-run working memory
# C_run = actual cost of _single_tournament_run, not assumed constant

SORT unique team names
ALLOCATE float32 reach[R, T, K]
FOR each run:
    CREATE a reproducible child random generator
    outcome = SIMULATE one tournament
    COPY each team's stage indicators into reach
MEAN reach over the run axis
BUILD team summaries and SORT by champion probability
RETURN table
```

The reach tensor alone occupies `4*R*T*K` bytes. The persistent dense Cholesky
factor also takes O(T_config²) memory; constructor factorization costs
O(T_config³). `memory_usage_mb()` reports that factor only, not total peak
memory. Runs are sequential, despite vectorized match sampling.

The generic default advances 24 teams into a simplified knockout with byes.
Do not label it an official 48-to-32 tournament forecast. More simulations
cannot repair that format assumption or validate the score inputs.
