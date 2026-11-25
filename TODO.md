# TODO — changes to apply to `main.ipynb`

No open item: every point below was applied on 25/11/2026, in the planned order (model comparison → structure × ρ → main model fixed by the decision rule → drift, importance sampling, tie-break → Metropolis MCMC → value of every match → N = 150 000 → wording, documentation, full re-execution). Rule from the instructor: model complexity must be **justified by numbers**; modelling may move to the foreground.

## Done (summary)

- **Audit (25/11/2026):** `ruff check` passes; a fresh-kernel run reproduces every stored output; the quoted numbers match the outputs.
- **Earlier text fixes:** figure order, §1.1 wording, references (Metropolis & Ulam, Owen), §2.4 ↔ §4.2 link, Poisson over-dispersion check, official 2025/26 tie-break order, 3^153 ≈ 10^73, K = 10 past seasons, kernel display name.

### 0. Choice of N — done (§2.5)
- Table N(ε, method, K = 1 / 18) computed in code; **N_MAIN = 150 000** derived from Hoeffding + union bound (ε = 0.005, N ≥ 131 586), defined in §2.5. Radii ±0.35 pt (one team), ±0.47 pt (18 teams).
- Hoeffding vs Chebyshev (27× cheaper, same guarantee, Chebyshev coverage 100%) and vs CLT (asymptotic, 59% coverage at Np ≈ 1) justified; N set by reporting precision, not by total uncertainty (elbow N = A/V ≈ 23, MC sd 10× below parameter sd from ≈ 2 300).

### 1. Model comparison — done (§2.6, §3.2, §4.2)
- `fit_poisson_model` gained a structure switch (M0–M4) and an optional ridge penalty; log-likelihood with log(x!) via `match_log_lik`.
- In-sample (153 matches): AIC → M3 (955.8), BIC → M1, LR tests all significant at 5% (M3 → M4: p = 0.035); 2025/26 second half (final check only): differences not significant.
- Backtest 2015/16–2024/25 (1 778 matches, log-score and Brier, season-clustered paired intervals): at ρ = 0, M3 ≥ M4 → **fallback would be M3**; with history, **M4 at ρ* = 0.6 is supported** (+0.019 ± 0.015 vs M3, +0.062 ± 0.024 vs within-season). Ridge (τ = 0.3) reported as a comparison point only.
- Consequence for the question: P(PSG) under M0–M4, with and without history (0.36 to 0.86).

### 1b. Value of every match — done (§3.5)
- Δ_m for the 153 matches with CRN on 40 000 common seasons; Lens – PSG is the largest (0.19), PSG matches ≈ 0.11, Lens matches ≈ 0.07; 86 significant swings; median variance ratio vs independent runs 23 (Figure 6).

### 1c. Multi-season fit — done (§3.2, §4.6)
- Structure × ρ compared on the backtest; multi-season M4 beats the within-season fit (9 seasons out of 10) → main model. Caveats (drift, Covid 2019/20, 20-team seasons, Paris FC, 2025/26 never used) stated.

### 1d. Other limitations — done (§4.7, §5.3, §3.3)
- Random-walk drift of strengths, σ chosen by backtest (Gauss–Hermite predictive): σ* = 0.03 not significant → static model kept; P(PSG) 0.857 → 0.839 with drift.
- Importance sampling with a cross-entropy proposal (one strength shift per team) for the outsiders: Lille/Lyon/Rennes validate against plain MC; Strasbourg 7.1e-6, Monaco 1.4e-5, Toulouse 7.5e-7, Angers 1.8e-9 with relative errors.
- Random-draw tie-break re-measured: 0.05% of seasons (head-to-head not implemented); weighted Pearson dispersion of the selected model 1.02.

### 1e. Metropolis MCMC — done (§2.8, §4.8)
- Theory (detailed balance, ESS, tuning, R-hat), vectorised random-walk Metropolis in numpy, toy Gamma–Poisson validation, wide-prior check vs MLE, trace/ACF/ESS/R-hat diagnostics, coverage with S (10.5%) vs ESS (90.5%), two-level Monte Carlo (posterior predictive 0.51, posterior sd 0.22 vs bootstrap 0.23). Kept as a complementary analysis. References Metropolis et al. (1953), Hastings (1970) added.

### 2. Statistical power — done (§4.5)
- Paired Brier sd ≈ 0.32 → about 290 matches (two half-seasons) needed to detect a gain of 0.036.

### 3. Documentation — done
- `CLAUDE.local.md`, `PLAN.md`, `README.md` updated (three-part research question, decision rule, final results).

### 3b. Notebook audit items — done
- Appendix cell asserts 53 quoted numbers; `N_MAIN` defined in §2.5; clipped intervals of rare teams explained in §4.2 with a pointer to §5.3; runtime ≈ 3 min.

### 4. Optional — done (§3.4)
- Exact enumeration of the 3^9 = 19 683 outcomes of the last matchday (scores 1–0, 0–0, 0–1) validates the ranking code: max error 0.0021 vs simultaneous radius 0.0056.
