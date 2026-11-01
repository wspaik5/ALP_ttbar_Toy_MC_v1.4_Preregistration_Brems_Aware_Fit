# ALP-ttbar Toy MC v1.4 — Preregistration: Bremsstrahlung-Aware Vertex Fits (Inner-Hit Fit and Gaussian Brems-Noise Fit)

**Author:** Won Shik Paik (Independent Researcher, Auckland, New Zealand)
**Date frozen:** 2026-11-02 (this file is committed to GitHub before any v1.4 result for ALP pairs or photon conversions is computed)
**Builds on:** v1.3, https://doi.org/10.5281/zenodo.23862775 (companion to arXiv:2610.38932)
**Status:** plan only. No v1.4 outcome exists at the time of this commit.

---

## 0. Why v1.4

v1.3 found that the conversion veto is decided by how the electron fit treats bremsstrahlung. With a fit that ignores
it (mode B, parallel constraint), fitted conversion vertices move outwards (median +14 mm) and the reach loss at 17 MeV
is 178×. Without bremsstrahlung (exploratory bound) the loss is ≈ 2×. v1.4 asks where two simple, brems-aware fits
place the answer between these two bounds. A full Gaussian-sum filter (ACTS, C++) is left for a later version.

## 1. Questions

- **Q1 (primary).** With a brems-aware fit, how large is the limit degradation from conversions at 17, 50, 100 and
  200 MeV (cτ = 1 mm), compared with v1.3 mode B (178×, 93×, 42×, 16×) and the no-brems bound (1.9×, 2.0×, 1.9×, 1.8×)?
- **Q2.** Does each fit remove the outward bias of conversion vertices and restore usable pulls?

## 2. Unchanged from v1.3 (frozen)

- Mini simulation (`minitrack.py`): geometry, material, Highland scattering, Bethe–Heitler bremsstrahlung, hits.
- **Identical simulated hits:** the same samples, seeds and simulation calls as v1.3 (conversions seed 5 / simulation
  seed 31; signal seed 11 / simulation seed 41), so v1.3 and v1.4 differ **only in the fit**.
- Fit framework of v1.3 **mode B**: joint two-track vertex fit with the parallel-at-vertex constraint (5 parameters),
  Jacobi-scaled Gauss–Newton, the same starting points and tie-break rules; scattering covariance along the true
  trajectory with the true momentum.
- Selection (`scan_v11`), pair-mass smearing, metrics (σ_core, f₃, pull width, degradation) as in v1.3.

## 3. The two fits (frozen)

**(a) Inner-hit fit.** Each track uses only its first **N = 6** measured hits (counted from the innermost hit), so less
bremsstrahlung has accumulated. Otherwise identical to v1.3 mode B. Sensitivity variants N = 4 and N = 8 are reported but
not used for the predictions.

**(b) Gaussian brems-noise fit** (single-Gaussian approximation of a Gaussian-sum filter, as in a Kalman filter with
bremsstrahlung process noise). For every material surface k crossed after the vertex, with effective thickness
t_k = t₀ cosh η and c_k = t_k / ln 2:
- **mean energy loss in the track model:** the curvature is multiplied by exp(c_k) after the surface
  (i.e. ⟨−ln z⟩ = c_k), so the predicted trajectory bends progressively more;
- **brems noise in the covariance:** a curvature change with variance ω_k² · c_k after the surface (Var(−ln z) = c_k),
  propagated to all later hits with numerically computed lever arms, added to the hit + scattering covariance.
The set of surfaces "after the vertex" is fixed per pair from the true vertex radius, as for the scattering term in
v1.3 (cosh η also from truth). All hits are used.

## 4. Sanity gate (new, from the v1.3 lesson; frozen)

Before any degradation is computed, for each fit and each sample: fit success ≥ 95%, and median σ_rep between 0.1 mm
and 100 mm. If the gate fails, the code is fixed, the failure and fix are reported, and the gate is re-run.

## 5. Validation (only samples independent of the outcome; criteria frozen)

- **W1 (b, single electrons with brems):** electrons from the origin, pT = 5–100 GeV, |η| < 1.2, independent seed.
  Median relative curvature bias of fit (b) within ±3%, and smaller in magnitude than for the brems-blind v1.3 fit.
- **W2 (b, no brems):** on the v1.3 V4 collinear pion-like sample, fit (b) with c_k set to 0 must reproduce v1.3 mode B
  (pull width 0.9–1.15).
- **W3 (a, no brems):** on the same V4 sample, fit (a) pull width 0.9–1.15.
If a criterion fails, the code is fixed and the fix is reported.

## 6. Predictions (written before running)

- **P1 (a, primary).** 17 MeV degradation between **30× and 150×** (modest gain: fewer layers of brems, but a shorter lever
  arm for the curvature that locates the tangent point).
- **P2 (b, primary).** 17 MeV degradation between **10× and 80×**.
- **P3.** Neither fit reaches the no-brems bound: both > 3× at 17 MeV.
- **P4 (b).** Median outward bias of conversion vertices (r < 800 mm) reduced to |bias| < 5 mm (v1.3 mode B: +14 mm),
  and f₃ between 10% and 40% (v1.3 mode B: 67%).
- **P5.** At 200 MeV, fit (b) gives ≤ 8× (v1.3 mode B: 16×).

Each prediction is reported as confirmed or refuted against these ranges. Refuted predictions are not re-framed.

## 7. Reporting

v1.4 note with a Figure 7 (residuals, pulls, degradation for v1.3 mode B, (a), (b) and the no-brems bound), the
prediction table, "Deviations from preregistration", "Limitations" and "Future directions" (ACTS Gaussian-sum filter as the
cross-check in a later version).

## 8. Limitations known in advance

All v1.3 limitations apply (transverse plane, barrel only, idealised layers, no pattern recognition, toy material map,
crude counting rule, selection optimised on the same samples). Fit (b) approximates the heavy-tailed Bethe–Heitler
distribution by one Gaussian, so it cannot follow large single energy losses; this is exactly what a Gaussian-sum filter
adds. Truth is used for the material list, cosh η and the scattering momentum, as in v1.3. Only trends and ratios are meaningful.
