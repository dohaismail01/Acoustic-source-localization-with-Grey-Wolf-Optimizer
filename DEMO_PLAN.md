# Live demo plan (4–5 minutes)

**Setup before the demo:** open `gwo_acoustic.ipynb` in Jupyter / VS Code. It is already executed, so
every figure is visible without waiting. Keep `figures/` open in a second window as a fallback.
The full *Run All* takes ≈ 7–8 min, so do **not** re-run everything live. Re-run only the three cells
listed below; each takes a few seconds.

| Time | Do this | Show | Key talking points |
|---|---|---|---|
| 0:00–0:40 | Scroll to **0b. Instance generation** and run it (≈ 1 s) | Printed box, true source, removed mics; then **Fig. 1** (3-D scene) | "One seed, 4612, generates everything in a documented order. Five unknowns: x, y, z, S, α. The obstacle stands 1.1 m from the source, and the 4 stress-test microphones are also chosen by the seed." |
| 0:40–1:20 | Scroll to **1. Mathematical formulation** (markdown) | Objective F, the penalty, and the λ rule | "f is the mean squared dB misfit, so it equals the ML estimate. Bounds are handled by reflection. Obstacle and α use a *graded* squared-depth penalty with λ chosen so 0.1 m of penetration costs 1 dB², the size of the noise floor. A death penalty would leave GWO a flat plateau with no direction back to feasibility." |
| 1:20–2:00 | Show the **2. Grey Wolf Optimizer** cell. Live: run `r = gwo(BASE, np.random.default_rng(4612)); r["x"], r["F"]` in a scratch cell (≈ 0.1 s) | Code: A = 2a·r1 − a, C = 2·r2, mean of 3 leader moves, `reflect` | "Vectorised over the pack, elitist α/β/δ, bounds after every update, and every evaluation is logged, so the budget is exactly 9,000." |
| 2:00–2:50 | Scroll to **Main experiment** output, then **Fig. 2** and **Fig. 3** | Table + p-values; convergence plot | "Same budget, same seeds. GWO's worst run beats random search's best: F 0.97 vs 2.76 dB², error 2.0 vs 4.7 m, p = 3·10⁻¹¹. The curves coincide for about 50 evaluations, then the swarm's information sharing opens a 1–2 order-of-magnitude gap." |
| 2:50–3:30 | **Fig. 7**, then **Fig. 6** | a(t) and \|A\|, diversity; leader paths | "\|A\| > 1 is only possible while a > 1, so it stops exactly at iteration 150. Leaders jump across the hall early and refine late. In sensitivity, an exponential decay is 2.4× faster but the least reliable. a0 = 2.5 is best." |
| 3:30–4:15 | **Fig. 9 / Fig. 14** (stress) and **Fig. 11** (S–α valley) | Errors vs σ with CRB bars | "Noise is the main stressor: the CRB scales with σ, while removing 4 microphones adds only 5% but couples S and α more tightly. At σ ≥ 3 the ML answer pins α at 0 and lowers S. The optimiser still hits its target 100% of the time; the *estimation* gets harder." |
| 4:15–4:50 | **Fig. 13** (ablation across instances) + **Fig. 15** (failure modes) | Bars N = 10 vs N = 30; obstacle-roof case | "Honest result: on our instance α-only is *more* precise. Three leaders are insurance: with 10 wolves they prevent the catastrophic run on instance 6612. Failure modes: too-fast a(t) decay, an ML optimum on the obstacle roof, and S–α–distance ambiguity." |
| 4:50–5:00 | — | Report conclusion | "GWO ≫ random search at equal budget, near-CRB at σ = 1. The limits come from the physics (coupling, vertical observability), not from the search." |

**Likely questions and short answers**
* *Why is success (≤ 1 m of x_ML) only 23%?* The valley floor is flat, so 1 m costs ≈ 0.05 dB². x_ML itself is ≈ 1 m from the truth and the CRB is 3.4 m, so this precision gap barely matters.
* *Why do errors fall below the CRB at σ ≥ 3?* The CRB assumes an unbiased, unconstrained estimator. The z and α bounds make our estimator biased but limit how far it can spread.
* *Is the comparison fair?* Yes. Identical 9,000-evaluation budget, objective, encoding box, per-evaluation logging and seed list (4612 + k), with 30 runs each. When the pack size changes, the iteration count is adjusted to keep the budget fixed.
