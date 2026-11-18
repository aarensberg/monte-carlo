# Lens or Paris? A Monte Carlo investigation of the 2025/26 Ligue 1 title race

Project for the course *Mathematics of Modern ML* (F. Blibeche) — Master 1 Data & AI for Finance, Mines Paris—PSL × Albert School.

**Authors:** Alessandro ARENSBERG, Amandine BARCELO, Charles DEFOSSEUX, Louis ALLIO

## Research question

At the 2025/26 Ligue 1 winter break, Lens led Paris SG by one point. What was the probability that Paris SG would still win the title? How precisely can Monte Carlo estimate it, and how much of the total uncertainty comes from the simulation itself rather than from the model fed to it?

The title race is a concrete, fully reproducible case study used to explain **what Monte Carlo computes, why it works, how precise it is, and where it stops being informative**. The score model (independent Poisson goals, fitted by maximum likelihood) is deliberately minimal: it is an input of the method, not the object of study.

## Main results

| | Result |
|---|---|
| Title probability at the winter break, within-season model | Paris SG 56.2%, Lens 40.8%, Marseille 2.7% — ±0.4 point simultaneously for the 18 teams, with N = 200 000 simulated seasons |
| Same model fed 10 past seasons (memory chosen by backtest, 2025/26 never used) | Paris SG 84.2%, Lens 13.1% — bootstrap range 0.63–0.95; 0.67–0.89 across the memories the backtest cannot distinguish |
| Simulation error vs model uncertainty | Parameter uncertainty (sd ≈ 0.23) is about 200 times the Monte Carlo error; adding past seasons divides it by almost 3, more simulations would not |
| Validation | The simulator matches an exact convolution computation; bias bounded below 0.001 over 4 million seasons |
| Variance reduction | Common random numbers: up to 117× fewer simulations; importance sampling: variance divided by ≈ 44 000 on a rare event of probability 4 × 10⁻⁶ |

## Contents

The deliverable is a single self-contained notebook, [`main.ipynb`](main.ipynb), structured as required by the assignment:

1. **Motivation** — data, standings at the winter break, precise questions
2. **Mathematical foundations** — estimand, Monte Carlo estimator, Chebyshev / Hoeffding / CLT confidence radii, union bound, law of total variance
3. **Implementation** — model fit, vectorised season simulator, exact validation, common random numbers
4. **Results and interpretation** — convergence and coverage, title probabilities, simulation vs model uncertainty, week-by-week re-estimation, calibration, more data vs more simulations (past seasons)
5. **Limitations and conclusions** — assumptions, rare events and importance sampling, answer to the research question

Data: Ligue 1 results from [football-data.co.uk](https://www.football-data.co.uk/francem.php) — 2025/26 in `data/ligue1_2025_26.csv`, 2005/06 to 2024/25 in `data/ligue1_history_2005_2025.csv` (the notebook downloads them only if these copies are missing).

## Running the notebook

Requires [uv](https://docs.astral.sh/uv/) (Python 3.14 is installed automatically).

```bash
uv sync                                                    # create the environment
uv run --with jupyterlab jupyter lab main.ipynb            # open and run interactively
uv run jupyter nbconvert --to notebook --execute main.ipynb --output main.ipynb   # full run on a fresh kernel (~2 min)
uv run ruff check .                                        # lint
```

Results are reproducible: every experiment uses its own seeded random stream, and dependency versions are pinned in `uv.lock`.
