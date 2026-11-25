# Lens or Paris? A Monte Carlo investigation of the 2025/26 Ligue 1 title race

Project for the course *Mathematics of Modern ML* (F. Blibeche) — Master 1 Data & AI for Finance, Mines Paris—PSL × Albert School.

**Authors:** Alessandro ARENSBERG, Amandine BARCELO, Charles DEFOSSEUX, Louis ALLIO

## Research question

At the 2025/26 Ligue 1 winter break, Lens led Paris SG by one point. What was the probability that Paris SG would still win the title? We answer in three steps:

1. **Model choice** — which model of the remaining matches do the data support, judged by numbers (in-sample criteria, likelihood-ratio tests, and a backtest on 10 past seasons) rather than by taste?
2. **Monte Carlo precision** — how precisely can Monte Carlo compute the title probability under that model, with a number of simulations fixed in advance?
3. **Uncertainty decomposition** — how much of the total uncertainty comes from the simulation itself rather than from the model?

The title race is a concrete, fully reproducible case study used to explain **which model the data support, what Monte Carlo computes with it, why it works, how precise it is, and where it stops being informative**.

## Main results

| | Result |
|---|---|
| Model choice | On the 153 first-half matches alone, the attack-and-defence Poisson model (36 parameters) is **not** supported: AIC prefers attack strengths only, BIC home advantage only, and the backtest finds attack-only as good. With 10 past seasons weighted by ρ^age (ρ* = 0.6, chosen by backtest; 2025/26 never used), the full model **is** supported out of sample (+0.019 ± 0.015 in log-score against attack-only, +0.062 ± 0.024 against the within-season fit) |
| Title probability at the winter break (selected model) | Paris SG 85.7%, Lens 12.0%, Marseille 1.5% — ±0.47 point simultaneously for the 18 teams with N = 150 000 simulated seasons |
| Choice of N | N = 150 000 from Hoeffding + union bound for ±0.5 point on all 18 teams at 95% (N ≥ 131 586); Chebyshev would need 27 times more |
| Simulation error vs model uncertainty | Parameter uncertainty (sd 0.073, bootstrap range 0.66–0.94) is about 80 times the Monte Carlo error; with the current season only it was 0.23. Memories the backtest cannot distinguish (ρ = 0.4–0.7) give 0.82–0.87 |
| Validation | The simulator matches an exact convolution and a full enumeration of the last matchday (3⁹ outcomes); bias bounded below 0.0006 over 4 million seasons |
| Calibration (2025/26 second half, never used to choose) | Brier 0.612 vs 0.667 for naive rules (gain 0.055 ± 0.044); better than the within-season model in log-score (+0.075 ± 0.067) |
| Variance reduction | Common random numbers: value of all 153 remaining matches (Lens – PSG is the most valuable, swing 0.19), up to 215× fewer simulations; importance sampling: variance divided by ≈ 46 000 on an exactly solvable rare event, and outsiders' title chances down to 10⁻⁹ with a cross-entropy proposal |
| Extensions | Drifting strengths not supported by the backtest (−2 points on P(PSG) if included); Bayesian shrinkage sampled by random-walk Metropolis (validated, ESS and R-hat diagnostics) improves forecasts but leaves the title uncertainty almost unchanged (posterior sd 0.22) |

## Contents

The deliverable is a single self-contained notebook, [`main.ipynb`](main.ipynb), structured as required by the assignment:

1. **Motivation** — data, standings at the winter break, five precise questions
2. **Mathematical foundations** — estimand, Monte Carlo estimator, Chebyshev / Hoeffding / CLT confidence radii, union bound and the choice of N, nested Poisson models and how to compare them, law of total variance, Markov chain Monte Carlo (Metropolis)
3. **Implementation** — maximum-likelihood fit, model choice (AIC / BIC / likelihood ratio / 10-season backtest of structure × memory), vectorised season simulator, exact validation, common random numbers and the value of every match
4. **Results and interpretation** — convergence and coverage, title probabilities and their dependence on the model, simulation vs parameter uncertainty, week-by-week re-estimation, calibration, sensitivity to the memory ρ, drifting strengths, Bayesian model sampled by Metropolis
5. **Limitations and conclusions** — assumptions, rare events and importance sampling (exact example, then the outsiders' title chances), answer to the research question

An appendix cell recomputes the 53 main numbers quoted in the text and fails if any of them no longer matches.

Data: Ligue 1 results from [football-data.co.uk](https://www.football-data.co.uk/francem.php) — 2025/26 in `data/ligue1_2025_26.csv`, 2005/06 to 2024/25 in `data/ligue1_history_2005_2025.csv` (the notebook downloads them only if these copies are missing).

## Running the notebook

Requires [uv](https://docs.astral.sh/uv/) (Python 3.14 is installed automatically).

```bash
uv sync                                                    # create the environment
uv run --with jupyterlab jupyter lab main.ipynb            # open and run interactively
uv run jupyter nbconvert --to notebook --execute main.ipynb --output main.ipynb   # full run on a fresh kernel (~3 min)
uv run ruff check .                                        # lint
```

Results are reproducible: every experiment uses its own seeded random stream, and dependency versions are pinned in `uv.lock`.
