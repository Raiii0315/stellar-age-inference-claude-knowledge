---
name: knn-ood-status
description: "k-NN OOD gate (age_inference_from_xp_spectra/knn_ood) — 1a chosen; RGB-only ref (ref_rgb_only/) done 2026-10-16: RC still passes 91 %, fails as RGB selector"
metadata:
  node_type: memory
  type: project
  originSessionId: 665650a4-7ccf-4a1a-b649-e71d242f760d
  modified: 2026-10-15T15:42:26.803Z
---

Session break on 2026-10-15. The user ended the session after task 1. Resume from task 2.

**Task 1 done: 1a (encoder mean) vs 1b (flatten → PCA). Decision: use 1a.**
- Code is in `<WORK>/age_inference_from_xp_spectra/knn_ood/`. It is outside the repo and not under git. README.md there has every result.
- Files:
  - `common.py`: `load_reference`, `load_targets`, `encode`, `knn_scores`, `pass_table`.
  - `01_mean.py`, `02_pca.py`, `04_testing_check.py`, `05_plot_compare.py` (2×3 pass / reject / reference figure).
  - Cached outputs are in `out/` (`emb_mean.npz`, `knn_*.npz`).
- Reference set A: 4,775 stars. Targets: 359,010 training_set rows; 227,776 of them have APOGEE labels.
- 1a results (τ95 = 8.04):
  - MS pass 0.8 %, giants (1.5–3.5) pass 39 %, purity 97.7 %.
  - Rejected giants are mostly high E(B-V) or faint. The gate measures "looks like Kepler stars", not Kiel position.
  - testing_set gives the same numbers, so evaluation stays on training_set.
- 1b results:
  - The planned setting (128 PCs, standardized) passes 28 % of MS.
  - With 16 PCs, or without standardization, 1b ≈ 1a (about +2 points on giants).
  - The cause is dilution: the MS signal sits in a few PCs, and standardization gives every noise PC equal weight. The explanation is on the Notion results page.
- Notion is up to date: the results page has 1a and 1b, and the plan page has task 1 checked. See [[notion-writing-rules]] for the page ids.

**Deep dive of 1a (2026-10-15, second session).**
- Quick check script: session scratchpad dd1.py, not saved to knn_ood yet.
- Rejection of giants is driven by G and E(B-V), both at the edges of the reference coverage:
  - giants with G 11–13 and E(B-V) < 0.2 pass at 0.8–0.9;
  - G 14–15 passes at ≤ 0.11 even at low E(B-V); G > 15 passes at 0.
  - Inside G < 13 & E(B-V) < 0.1, the rejected stars are metal-poor ([M/H] < −1 passes at 0.12) or at the Teff/logg edges.
- User's observation: in the Kiel plot the rejected stars also fill the pass region.
- **NEXT TASK (user, 2026-10-15): make plots in axes other than Teff/logg** to check whether pass/reject really separates RGB/RC/AGB from everything else (no evstate for targets).
  - I recommended:
    - an extinction-corrected CMD (M_G or M_K vs (BP−RP)_0, from parallax, bprp, jk and logc19 in the h5);
    - a Kiel plot restricted to G and E(B-V) bins inside the reference range, as a control;
    - a metallicity-corrected ΔTeff vs logg plane (Bovy+2014 RC style), with the reference evstate as the calibration;
    - [M/H] vs logg, to tell population from evolutionary state;
    - nuisance maps of G vs E(B-V), and l, b.
  - [C/N] and [α/M] are not in the h5, and no allStar is available locally.
  - The plan is written in the 2026-10-15 daily log (id 3fa37d5c362180ebb4c4ce1c340761f1), toggle 今日やること：k 近傍の判定（1a 平均）の pass / reject は正しいか.
  - Scope: 1a only. The question is whether the rejects are correct or are missed RGB/RC/AGB stars.
  - Method 1: a Kiel plot restricted to the reference G and E(B-V) range.
  - Method 2: non-Kiel axes.
  - First step: method 1 plus the CMD.
- **Method 1 done (2026-10-15)** in `knn_ood/mean_range/` (scripts load.py and 01–03; results in its README).
  - Restricting G and E(B-V) to the reference 1–99 % range raises the pass rate in the Kiel core from 48 % to 83 %.
  - In the tighter box 11 ≤ G ≤ 13, E(B-V) ≤ 0.15, with [M/H] ≥ −0.7, it reaches 95.4 %, the same as the reference stars themselves.
  - So the in-region rejects are explained by G, E(B-V) and [M/H]: the gate is correct as an OOD detector, but it misses faint, reddened and metal-poor RGB/RC.
  - Not yet written to Notion.
  - Next: the CMD (method 2-1).
- The user said they couldn't tell whether the rejects are "correct" or misses, so I added 04_classifier_check.py.
  - It uses prelim_xp_rgb_kiel's RGB/RC classifier on the evstate-less giants and measures the fraction of clump-region stars predicted RC.
  - PASS: 0.68 (Kepler 0.76). REJECT: 0.28. REJECT with E(B-V) > 0.3: 0.15. REJECT faint or in-range: 0.47–0.48.
  - Conclusion: the rejects are correct for "keep only stars where the classifier is trustworthy", and are misses for "collect all RGB/RC".
  - Results are in the mean_range README.
  - The user then said to forget the classifier check and the validation discussion, which are not on Notion.
- Notion page for the analyses 1 (Kiel plot in range) and 2 (G × E(B-V)): id 3fa37d5c362181289df6c32449598699. Next axes go there.
- **Analysis 3 done (2026-10-15):** ΔTeff vs logg, in `mean_range/05_dteff.py`.
  - Teff_RGB = 4583 + 461(logg − 2.5) − 312[M/H], fit to the reference RGB; the residual is 72 K.
  - Pass and reject both show the RGB ridge and the RC clump. At logg 2.3–2.6, the fraction with ΔTeff > 100 K is 0.66 for pass and 0.67 for reject, so the gate is neutral between RGB and RC.
  - Not on Notion yet.
  - The CMD was postponed. It is only useful for unlabelled stars, and its correction error is largest for the rejected stars.
- **Analysis 4 done (2026-10-15):** [M/H] vs logg, in `06_mh_logg.py`.
  - With G, E(B-V) and the Kiel core matched, the pass rate is about 0.95 for [M/H] ≥ −0.7 and falls steeply below that: 0.70 at −0.9 and about 0 below −1.2.
  - So metallicity is the remaining reason for rejects.
  - Analyses 1–4 are all on the Notion page 3fa37d5c362181289df6c32449598699 (added 2026-10-15), along with a combined summary for 1–4 and a section of remaining candidates.

- 2026-10-15, late: the user asked for explanations of the dteff_hist_clump, mh_logg and mh_passfrac figures. They want to use [α/M] and [C/N] next, on the idea that XP may carry them. For that, I made data_files/*_abund.h5 (see [[abund-h5-copies]]). No [α/M] or [C/N] pass/reject plots exist yet. The user then paused this session (id 579f5c2c-14b7-41c2-a3bd-a574a52c36dd) to branch off into a slightly different discussion and wants to keep this one.

**Next steps (after the plotting task):**
- Task 2: same reference A.
  - Space 2: raw `xp_coeffs_gnorm` 64.
  - Space 3: Spec2Labels-predicted Teff/logg, optionally +[M/H].
  - Use the same figures and tables (05_plot_compare style) and compare against 1a.
  - Spec2Labels predictions can run on CPU (see [[cpu-inference-ok]]).
- Task 3: design B (RGB-only reference, 2,670 stars) with 1a, compared with A, plus the AUROC of d_k for RGB vs RC.
- ideas.md 5.3 does not have the 1a/1b results yet. Add them if the user wants, and commit only when asked.

**Run gotchas:**
- Don't use `python -I`; it breaks the local `common` import.
- On this 128-core machine, set `OPENBLAS_NUM_THREADS=16 OMP_NUM_THREADS=16`, or the k-NN crashes.
- For CPU runs, set `CUDA_VISIBLE_DEVICES=""`.

- **2026-10-15, later: the user's actual question is definition A of a miss** (a giant/RGB/RC/AGB that was rejected), and whether the k-NN result alone can identify misses or other information is needed. It is not "is the classifier reliable on the rejects".
  - Checked in `mean_range/07_miss_dk.py`: among the rejects, the AUROC of −d_k for giant vs logg ≥ 3.5 is 0.55 overall. It is 0.81 at E(B-V) < 0.1 and inverts to 0.31 at E(B-V) > 0.6.
  - A second threshold that recovers 30 % of the rejected giants lets in 17 % dwarfs.
  - Answer: d_k alone cannot identify the misses, so a giant-vs-dwarf signal from outside the k-NN is needed. Not on Notion yet.
  - The user wants only the Kepler evstate labels (high accuracy) as the label source. I proposed augmenting the Kepler XP with simulated reddening and faintness noise. The user has not decided yet, and the giant definition is also open: (a) the reference Kiel range/contour or (b) logg < 3.5 & Teff < 5500 K.
  - All of this is in the 2026-10-15 daily log, toggle 今日の結果, written as sections 1–6 plus 次にやること. 07 is not yet on the analysis page 3fa37d5c362181289df6c32449598699.

**2026-10-16: RGB-only reference (task 3) done** in `knn_ood/ref_rgb_only/` (01_knn.py, 02_plot.py, README). The user asked for an RGB-only reference instead of a 2-stage approach. It reuses emb_mean.npz, so no encoder rerun. Comparison is called "ref RGB+RC+AGB" (files: *_vs_ref_rgb_rc_agb).
- τ95 = 8.69. Held-out Kepler RC passes at 0.913, AGB at 0.84. AUROC RGB vs RC is 0.63.
- training_set pass rates ≈ ref RGB+RC+AGB. In the clump band, the RC-like fraction of pass goes from 0.66 to 0.64.
- Conclusion: an RGB-only reference cannot select RGB in one step. Not on Notion yet.
- Same figure set made for the RGB-only reference (2026-10-16). The mean_range scripts 01/02/03/05/06 now take the env var `KNN_REF=rgb_only`; output goes to ref_rgb_only/figs with the suffix `_ref_rgb_only`. The G/E(B-V) range and the Kiel core box are kept the same as before.
  - The shapes are almost identical to the RGB+RC+AGB reference.
  - In range, the clump-band rejects are slightly more RC-like (0.74 vs 0.68), but the RC-like fraction of the passes only goes from 0.66 to 0.65.
  - The user finds histograms of little value; prefer Kiel plots and other 2-D axes.
- The side-by-side comparison is in `knn_ood/compare_ref_rgb_only_vs_ref_rgb_rc_agb/compare.py`. Each figure has the top row = ref RGB+RC+AGB and the bottom row = ref RGB only, columns pass / reject / reference, and the same colour scale in both rows. The axes are Kiel, ΔTeff–logg, [M/H]–logg and G×E(B-V), plus mh_passfrac.
- The user asked that in RGB-only figures the reference panel shows RGB only, not RC/AGB. load.py `REF_EVSTATES` handles this, and `load(knn_ref)` can read either reference.
- User's observation (2026-10-16): the hot clump region (logg ≈ 2.5, Teff 5000–5400) has no reference RGB, yet its stars pass. I checked this in `ref_rgb_only/03_neighbours_kiel.py`.
  - Pass rate in that region is 0.41.
  - Their 10 encoder-space neighbours are the hottest reference RGB, about −135 K and +0.38 dex in logg from the star.
  - In general, neighbour logg is compressed near the clump (own logg 2.4 → neighbour logg 2.65).
  - So the encoder-mean distance does not respect Kiel boundaries, especially along logg.
- Why the RGB-only reference passes more stars (`04_why_more_pass.py`): τ95 rises ×1.08 because RGB lose their RC neighbours, while the d_k of stars with RGB-only neighbours rises only ×1.014. So the threshold loosens everywhere (new passes include 393 MS and 384 upper RGB), and it tightens only near RC. Standardisation is not the cause.

**2026-10-16, later session: what the k-NN pass/reject is based on.** User's goal: learn what 1a k-NN distance reflects (and so what the encoder latent carries); RGB-only k-NN passes RC at 91 % (AUROC 0.63). I only PROPOSED k-NN as OOD gate + Kepler-trained RGB/RC classifier; the user has NOT decided the approach — never describe it as decided.
- `mean_range/08_logg_vs_x.py`: logg vs E(B-V), G, [α/M], [C/N]; top = targets pass blue / reject orange, bottom = reference coloured by evstate only (user: no pass/reject labels on reference; RC purple here to avoid clash). Both refs done.
- `09_clip_steps.py`: clip targets to reference 1–99 % range axis by axis, **greedy order only** (user had fixed order deleted), Teff and logg separate, last step = 7-label-space k-NN ("joint"; user found it confusing — offered to drop/rename, no answer yet). RGB-only: logg 0.416 → G 0.717 → E(B-V) 0.769 → [C/N] 0.797 → Teff → [α/M] → [M/H] 0.816 → joint 0.873; reference itself 0.95 → 0.976 (rejected refs sit at label edges, 51 % of them clipped vs 10 % of passes).
- `10_residual_rejects.py`: remaining rejects have higher E(B-V), G at box edges; AUROC from 7 labels 0.83, +S/N/plx/colours 0.88 → residual mostly same labels (density edges), little hidden info.
- User needs table columns explained explicitly (they found 残る星/pass/参照の星自身 unclear). Not on Notion yet.

**2026-10-16: directory reorganised.** Top dir renamed `rgbrc_prelim` → `<WORK>/age_inference_from_xp_spectra` (symlink rgbrc_prelim deleted 2026-10-26). a1–a12 + their figs in `00_early_checks/` (names kept; user calls them junk). knn_ood layout now: compute scripts 01–05 + `06_knn_ref_rgb_only.py` + common.py at top; all plotting/analysis in `analysis/` (load.py, 01–10 = old mean_range, 11–13_rgbonly_* = old ref_rgb_only/02–04, 14_compare_refs.py); outputs only in `results/{ref_rgb_rc_agb,ref_rgb_only,compare_rgb_vs_ref-all}/{out,figs,logs}` (user chose the name compare_rgb_vs_ref-all). common.py has KNN, RESULTS, OUT (= results/ref_rgb_rc_agb). Older paths in this memory (mean_range/, ref_rgb_only/ scripts) refer to the pre-reorg layout.

**2026-10-19: G-band correction started.** User decided: augment by degrading the reference XP (add noise matching fainter-G errors); do not touch target XP (noise can't be removed). Dir `knn_ood/g_band_correction_augmentation/` (scripts + README with plan), outputs `knn_ood/results/g_band_correction_augmentation/{out,figs,logs}`. Facts found: giants E(B-V)<0.1 pass by G — 6–8: 0.02, 9–10: 0.69, 11–12: 0.91, 13–14: 0.53, 14–15: 0.085, >15: 0; gnorm err flat (floor) for G<11, RP err ×3 at 14–15, ×9 at 16–17.5 vs 11–12 → bright-side reject not noise-driven (handling undecided). First step proposed, not run: degrade ref to G 14–15 and check pass drops to ~0.1. Leave-group-out for copies, G-binned τ / MS leak check needed.
- 2026-10-19 later: augmentation procedure fixed (user: "実際に行いたい") and written to Notion page 3fe37d5c362181d381d7fc434d87f17d §5 + g_band_correction_augmentation/README.md: raw xp_coeffs (110) × k=10^(-0.4(G'-G)) + eta, sigma_add² = sigma(G')² − k² sigma_ref² (my earlier formula without k² was WRONG), sigma(G') from training_set gnorm_err median (|ΔG|<0.25, |Δbprp|<0.1) × F_G'; divide by F_G'=kF_G → k cancels, only noise reaches encoder (user found this confusing; explain via the formula). GaiaXPy checks 1 (ΔG_syn = G'−G, colour unchanged), 2 (flux err/flux matches real G' stars per wavelength), 3 (100 realisations' std = stated err). GaiaXPy 2.1.4 is ALREADY in the repro uv env (utils/gaia_utils.py xp_coeffs_phys converts coeffs→nm spectrum); no separate venv. Next: 20 ref stars × G'=14,15,16. Nothing run yet.
- 2026-10-19: user wants the augmentation done ONE STEP AT A TIME (Steps 1–5: σ table → degrade few stars + spectra/GaiaXPy checks → whole ref to G14–15 vs pass 0.21 → flat ref with per-bin τ & group leave-out → re-judge). Step 1 DONE: 01_sigma_table.py → out/sigma_table.npz; all 84 bins in G 9.6–16.3 × ref colour 1.11–1.67 have ≥88 stars. Notion page G バンド補正 Step 1（19/10/2026）id 3fe37d5c362181559ecaf5a53f7c82db (after the plan page); plan page 次にやること is now a Step 1–5 checklist. Next: Step 2, ask before starting.
- 2026-10-19: Step 2 DONE. degrade.py (degrade(), SigmaTable with log-interp in G) + 02_check_degrade.py → results/.../step2. All checks pass (synthetic G shift exact, relerr ratio 0.92–1.09, realisation scatter 0.97–1.01). GaiaXPy input: DataFrame with bp/rp_coefficients, _coefficient_errors, _coefficient_correlations=np.eye(55), _standard_deviation=1, _n_parameters=55, _basis_function_id 56/57; set plt.rcParams['text.usetex']=False (gaiaxpy turns LaTeX on, not installed). Some ref stars lack APOGEE labels in h5 → use fits labels from load_reference. Notion Step 2 page id 3fe37d5c362181c485ace569d6b2d24f; user wants every symbol defined (what k is, how decided). Next: Step 3 (ask first).

- 2026-10-19 reorg (user chose flat scripts): g_band_correction_augmentation/{README.md, degrade.py, 00_g_distribution.py, 01_noise_table.py, 02_degrade_check.py}; outputs results/g_band_correction_augmentation/{g_distribution, noise_table, degrade_check}/{out,figs,logs}. Older names (g_hist/, 01_sigma_table.py, 02_check_degrade.py, step2/, top-level out/figs/logs) are gone. User dislikes step-numbered dir names like step2 — name dirs by content.

**2026-10-20: bias/variance check (the boss's "c vs norm flux").**
- Script: `g_band_correction_augmentation/03_bias_variance.py`. Notion page id 3ff37d5c36218182b756c013ef69484b, under the G バンド補正 theme page.
- High orders (n ≥ 6): variance only, consistent with noise.
- Low orders (n ≤ 5): faint giants are redder by about 1 bright-star spread, smoothly with G. This is likely extinction or population, not a normalisation bias.
- Augmentation assumption OK. Next: Step 3 (degrade the whole reference to G 14–15 and check the pass rate is about 0.21).
- Still unconfirmed with the boss whether "c" means the coefficient value or the coefficient index; both plots were made.
- **Redone as 04_bias_vs_noise.py (2026-10-20, the boss's actual point):** since the correction adds variance, the bias can be ignored if |Δ| < σ_faint. MS and giants are compared separately (logg/Teff cut); x = bp0…, y = c_n/F_G symlog box plots. Result: ignorable for most encoder coefficients (median |Δ|/σ 0.24 for giants, 0.19 for MS). Not ignorable only for low orders n ≤ 6 and rp12, where faint stars are redder. The same Notion page was rewritten (title now ends "bias はノイズより小さいか"). The user dislikes extra derived figures (dashed expectations, ratio plots): keep only what was asked and put the numbers in tables.
- **Final form (2026-10-20):** `g_band_correction_augmentation/03_bias_vs_variance.py`, giants only, output in `results/.../bias_vs_variance/`. The old 03/04 scripts and the bias_variance/ and bias_vs_noise/ dirs were deleted. The user wants the giant selection and the bright/faint G split stated explicitly wherever results are reported.
- **2026-10-21: boss feedback on bias_vs_variance** (user could not answer in the meeting; needs c_n vs flux and σ vs box-spread explained). Boss: giant cut too strict (bright 5,663 / faint 206) → use standard Teff 4000–5500, logg 1–3.5; box plots spread unexpectedly; main concern = skewness in the c_n boxes vs adding Gaussian noise; suggested just running k-NN with the noise-added reference. Today's plan given: (1) explanation, (2) redo with wide cut on linear axis + "degraded bright vs real faint" distribution comparison (symlog likely exaggerates skew; box = star-to-star spread + noise, σ = noise only), (3) Step 3 k-NN on degraded reference (CPU).
- 2026-10-21 done (kept 03, added): 04_bias_vs_variance_wide_cut.py (wide cut 48,730 giants; degraded bright matches faint IQR for n≥7, core skew small, faint tails heavier in rp1/rp3/rp10/rp12; low-order offset = population, faint [M/H] −0.50 vs −0.25) and 05_knn_degraded_reference.py (Step 3: ref degraded to G' 13–16, original τ95; matched Teff 4500–4900/logg 2.3–2.7/[M/H] −0.3–0.2: degraded ref 0.79/0.17/0.00 vs real 0.81/0.21/0.00 → noise explains it; broad comparison is offset ~0.3 mag due to population). Notion done 2026-10-21: Step 3 page 40037d5c362181818fa1e68bdd6dffc2 (with worked numeric example of the noise injection), 04 added as section 追加（21/10） on the bias page, policy-page checklist + theme summary updated. Not in the 10-21 daily log. Next: Step 4 (copies in reference + per-G τ).
- 2026-10-21 later: user wanted the ref_rgb_only-style figure set (Kiel, mh_logg, G×E(B-V)) with the noise-augmented reference AS the reference, plus Kiel no-noise vs noise comparison; always state which evstates are in the reference. Done: g_band_correction_augmentation/06_knn_ref_noise_augmented.py (ref = Kepler RGB+RC+AGB + 33,206 noise copies G' 13–16.5, per-G τ95, group leave-out) → results/ref_noise_augmented/; analysis/load.py has KNN_REF=noise_aug (d = d_k/τ(G), τ=1); 14_compare_refs.py has COMPARE_WITH=noise_aug → results/compare_noise_aug_vs_ref-all/. Matched giants pass ~0.93–0.98 up to G 15; MS faint leak 0→~0.02; bright side pass drops (smaller τ). Not on Notion yet.
- 2026-10-21: RGB-only noise version done (06 with AUG_REF=rgb_only → results/ref_rgb_only_noise_augmented/; load.py KNN_REF=rgb_only_noise_aug; 14 with COMPARE_BASE=rgb_only COMPARE_WITH=rgb_only_noise_aug → results/compare_rgb_only_noise_aug_vs_rgb_only/). Kepler RC still pass 0.90, AUROC RGB vs RC 0.64. Gotcha: never pipe long plot scripts into `head` (SIGPIPE kills them mid-run).

- 2026-10-21 clip_steps on augmented refs (analysis/09 with KNN_REF=noise_aug / rgb_only_noise_aug; ranges from all aug rows, copies at G'; 06 now saves d_aug): G no longer matters (without-G pass 0.74/0.73 vs all-cuts 0.73/0.72; before, without-G 0.62/0.63 vs 0.81/0.82). E(B-V) becomes the dominant axis (without E(B-V) 0.53/0.52). All-cuts pass drops 0.81→0.73 because box now admits G up to 16.5 (N 34k→46k). joint 0.82.

**2026-10-22: G-band correction redesign.** Plan (Notion daily log 2026-10-22, toggle 今日やること, items 1–6): understand current method → 64-coeff σ–G plots → select noise stars by Teff/logg instead of BP−RP → fit σ_{c_n}(G)=f(G) continuously → make reference G distribution flat → GaiaXPy check of G'.
- Giant cut DECIDED by user: Teff 4000–5500 K, logg 1–3.5 (based on 2024MNRAS.528L.122C).
- Use raw σ (xp_coeffs_err) for the new fit, not gnorm. GOTCHA: 33,457 training_set rows without XP have xp_coeffs NaN but xp_coeffs_err = 0 → require finite xp_coeffs.
- Done: 07_g_distribution_ref_noise_augmented.py (→ g_distribution/figs/g_hist_ref_noise_augmented.png, same format as g_hist.png; user wants new figs placed in the existing matching dir and styled to match). 08_sigma_vs_g_coeffs.py (→ noise_table/figs/sigma_vs_g_{bp,rp}.png, linear y, full G; medians smooth, no steps). Old 6-coeff sigma_vs_g.png and its code in 01 deleted at user's request.
- KEEP 01_noise_table.py + sigma_table.npz (BP−RP version) for comparison; user agreed. New Teff/logg fit goes in a new content-named script/dir; degrade.py to get a switch between the two. Renumber scripts only when adding the new version.
- **2026-10-22 later — TODO FOR 2026-10-23 (user: 「明日やるので忘れないで」).** Decisions are in g_band_correction_augmentation/README.md, section 「コピーの作り方の作り直し」:
  1. Implement the new copies: G' 9–17; flat in 0.1-mag bins (80 bins), each bin = originals + copies = 240 (fraction 1/80; max originals per 0.1 bin = 237 at G 12.8–12.9); keep all 4,775 originals; 14,427 copies, 19,200 rows in 9–17 (+2 originals below G 9). Total = the minimum that satisfies flat + keep-all (more copies only add repeats; τ is self-calibrated).
     Source stars: those with G < bin upper edge; spread m copies evenly over n sources (⌊m/n⌋ each, remainder random) — no switch. G' ~ U[max(G, lo), hi]; G'≈G allowed; RGB/RC/AGB mix not balanced. Bright end has few sources (G' 9.0–9.1: 3 stars ≈80 uses each) — build as is, then check.
     Noise σ: 09_sigma_fit.py degree-3 fit, σ_n(G) = exp(poly3(G−13)) on absolute σ (out/sigma_absolute/sigma_fit_poly3.npz). Write it as exp in notes; fit in log. τ bins still undecided (do later; user said irrelevant while building the noise).
  2. **Then verify with GaiaXPy**: convert the copies' XP → synthetic G and check with a histogram that the copies are dimmed correctly (synthetic G matches G').
  Other 10-22 changes: 00_g_distribution.py + g_hist.png deleted → 00_n_stars_vs_g.py (giants Teff/logg cut + reference, 0.1-mag bins, grey outside G' 9–17, output g_distribution/figs/n_stars_vs_g.png). 09_sigma_fit.py added (fit curves on sigma_absolute figs, *_fit_poly3.png). Degree comparison: holdout rms deg3 2.4 %, deg6 1.4 % (plateau); user chose deg 3 for now.
- **2026-10-23 pre-implementation decisions (implement in a NEW session; recorded in README block 「実装前に追加で決めたこと」):**
  - Source rule stays "G < bin upper edge", order n first → ⌊m/n⌋ + random remainder → G' ~ U[max(lo, G_i), hi] per copy (user confirmed). In-bin sources skew G' upward → log it.
  - Coeffs not used by the encoder (bp/rp 32–54, h5 idx 32–54 & 87–109; user said "index 33以降" counting from 1): NO noise, only ×k (needed so GaiaXPy gets all 110).
  - GaiaXPy check: only figure (ii) = histogram of synthetic G of all 19,200 rows (noise-added copies' XP → G) overlaid on flat G'. Skip the per-copy difference plot for now.
  - GaiaXPy XP→G (synthetic photometry: F = Σ c_n w_n, w_n = ∫φ_n T_G dλ) was explained to the user on 2026-10-23; no need to repeat.
  - Reference = RGB+RC+AGB only this time; RGB-only version WILL be done later (240 changes). k-NN (encode, τ bins) later.
  - Planned names: 10_flat_copies.py, 11_flat_copies_synthetic_g.py, output results/g_band_correction_augmentation/flat_copies/; new seed; add a poly3-fit σ reader to degrade.py keeping SigmaTable.
  - **Names/dirs UPDATED (supersede the line above):** scripts 10_noise_copies_fitting.py, 11_noise_copies_fitting_synthetic_g.py; new-version dirs get suffix `_fitting`; per-ref subdirs rgb_rc_agb/ (later rgb_only/). Keep old version (06/07 + outputs) untouched.
  - **FIRST TASK next session, before 10: reorganise results/g_band_correction_augmentation/ into noise/ (noise_table, noise_copies_fitting), checks/ (g_distribution, degrade_check, bias_vs_variance, bias_vs_variance_wide_cut, synthetic_g_fitting), knn/ (knn_degraded_reference + MOVE results/ref_noise_augmented, ref_rgb_only_noise_augmented, compare_noise_aug_vs_ref-all, compare_rgb_only_noise_aug_vs_rgb_only; later ref_*_noise_augmented_fitting).** No-noise kNN dirs stay in results/. Move only, no recompute. Fix paths in g_band 00–09 + degrade.py, analysis/load.py, analysis/14_compare_refs.py, analysis/01_kiel_range.py, 05_plot_compare.py; verify paths exist; redraw README tree. Full tree is in README block 「出力フォルダの整理」.
- **2026-10-23 DONE (same session):** results reorganised into g_band_correction_augmentation/{noise,checks,knn}/ (paths fixed in 00–09, degrade.py, analysis/load.py, analysis/14; all verified). degrade.py: SigmaFit + degrade_fitting (46 non-encoder coeffs ×k only). 10_noise_copies_fitting.py (seed 20261023): 14,427 copies, 240/bin, clipped σ_add 16.6 %, bright-end uses 80/26/10, G' skewed upward in bins 9.0–9.3. 11_noise_copies_fitting_synthetic_g.py: synthetic G flat 231–248 per 0.1 mag; synthetic−catalogue G −0.003 mag; copy shift matches G'−G to ±0.0004 → dimming correct. NEXT: encoder + k-NN with τ bins (undecided) → knn/ref_noise_augmented_fitting; RGB-only version later.
- **2026-10-23 RESULTS LAYOUT (user: split by what was done; supersedes the noise/checks/knn layout above):** knn_ood/results/
  knn/{original/{ref_all, ref_rgb_only}, noise_augmented/{ref_all, ref_rgb_only} (old BP−RP σ), [later noise_augmented_fitting/{ref_all, ref_rgb_only}], degraded_reference (05), compare/{original_rgb_only_vs_original_all, noise_augmented_all_vs_original_all, noise_augmented_rgb_only_vs_original_rgb_only}};
  g_distribution/ (00, 07, 11 — 11 files suffixed _noise_augmented_fitting_ref_all); noise/{noise_table, noise_copies_fitting/{out,logs}/ref_all}; noise_checks/{degrade_check, bias_vs_variance, bias_vs_variance_wide_cut}.
  common.OUT = results/knn/original/ref_all. Names use ref_all (not rgb_rc_agb) for RGB+RC+AGB. All script paths fixed and verified; READMEs (knn_ood, analysis, g_band) updated.
- 2026-10-23: results/noise/noise_table split (user: name unsuitable) → noise/sigma_table_bprp/ (01, old BP−RP table sigma_table.npz + count/ratio figs) and noise/sigma_vs_g/ (08 sigma_absolute|relative, 09 sigma_fit_poly3.npz). degrade.TABLE/FIT updated.
- 2026-10-23: g_distribution figs renamed by what they show: g_hist_original.png (00, before noise), g_hist_noise_augmented_ref_all.png (07, old copies), g_hist_noise_augmented_fitting_ref_all.png (11, new copies; 2 panels counts(log)/fraction, overlays training_set all 359,010, giants Teff 4000–5500/logg 1–3.5 129,264, ref before noise, target G/G' dashed, synthetic G). noise_copies_fitting has no fig (user: not needed for now).
- **2026-10-23 k-NN with fit-σ copies DONE:** g_band…/12_knn_ref_noise_augmented_fitting.py → results/knn/noise_augmented_fitting/ref_all/ (same npz keys as 06); provisional τ bins 0.5 mag ≤9.5…≥16.5 (τ formally undecided). analysis/load.py KNN_REF=noise_aug_fitting (suffix _ref_noise_augmented_fitting); ran 01, 02, 06, 09; 14 COMPARE_WITH=noise_aug_fitting → knn/compare/noise_augmented_fitting_all_vs_original_all/. Results ≈ previous version: matched giants G14–15 0.921 (old 0.932), giants E(B-V)<0.1 G14–15 0.582 (0.645), G16–17 0.238 (0.097); bright side G11–13 drops less; MS faint 0.015–0.034. Bright-end reuse visible as a ref contour blob at E(B-V)≤0.003, G 9–10.5.
  - Planned (user, later): split results/knn/*/figs into subdirs (DONE 2026-10-23 for original/*, noise_augmented/*, noise_augmented_fitting/*: figs/ top = kiel_range_full, kiel_range_giants, g_ebv_pass, logg_vs_x, mh_logg, clip_steps; figs/else/ = kiel_compare_*, clip_steps_kiel, dteff_*, mh_passfrac; load.py makes figs/else; ref_rgb_only analysis/11,12 figs (dk_hist/dteff_clump *_vs_ref_rgb_rc_agb, kiel_ref_rgb_only, neighbours_kiel) also in else/ (user chose this over moving to compare/); user: no kiel/ subdir; compare/ dirs also split 2026-10-23 (figs/else = dteff_logg_*, mh_passfrac; 14 writes there); move ref_rgb_only *_vs_ref_rgb_rc_agb figs to knn/compare/original_rgb_only_vs_original_all). 07_miss_dk deleted 2026-10-23. 03_plot.py and its kiel_pass_reject_{mean,pca,pca16raw}.png deleted 2026-10-23 (user: not needed; kiel_compare_* covers it).
- 2026-10-23: analysis/15_pass_vs_g_noise.py → knn/compare/noise_augmented_fitting_all_vs_original_all/figs/pass_vs_g.png (pass vs G, 3 gates, fixed star sets: loose giants E(B-V)<0.1 / fit-version joint set). 09 now saves out/clip_steps{SUFFIX}.npz (box, joint masks); first step renamed "no cut (stars with APOGEE Teff, logg)". Finding: on the same stars noise does NOT increase misses at G 9.5–13.4 (fit-version joint set: 0.853 vs 0.854); lower overall pass of fit version comes from including faint stars (G box 9.1–16.9 vs 9.5–13.4). Fit version is 2–6 pts below previous noise version at G 13–15, and 3–6 pts below no-noise at G 11–13.
- 2026-10-23: user decision: the "in range" cut (load.py in_range, used by 01, 02, 04, 05, 06, 12, 14) now uses fixed G 9.0–17.0 (the copies' G' range) for every reference, E(B-V) still ≤ reference 99 % (0.395). Only the bottom/in_range rows change; the "all targets" rows stay uncut. Before: G = reference 1–99 % (9.52–13.40). analysis/README old numbers in the 01 results section are pre-change.
- Plan for Monday 2026-10-26 (user, written to Notion daily log 2026-10-26, id 40237d5c36218119bb5dd5e31d3a0d7a): (1) question: high-E(B-V) stars are rejected — is that OK, or does extinction need a correction like the G band? (2) train XGBoost on the noise-added XP (fit-σ copies) and extract RGB from the k-NN-passed training_set stars; (3) visualise it.
  - 2026-10-26: user added question 1-2 to that log (section 1 now 「質問」 with 1-1 extinction, 1-2): k-NN misses of giants will likely remain — is that OK, and what fraction of likely giants in training_set may we miss?

**Figure giant cut changed (2026-10-26, user):** figures that selected giants with 1.5 ≤ logg < 3.5 now use 1 ≤ logg < 3.5. This covers analysis 02, 05, 06, 12, 14 and g_band 05, 07; the logg axes now start at 1.0. Nothing else changed: no Teff cut was added, and log-only tables (01_mean, common.pass_table, etc.) keep 1.5. Ref_all giants: 135,813 stars, 34.1 % pass (was 117,928 stars, 38.9 %).
- **2026-10-26: why faint (G 15–16) joint-set giants pass < 50 % (fit-σ ref_all)** — quick check, session scratchpad faint_check.py only (not saved in knn_ood, not on Notion). Mostly composition: faint joint-set stars sit at E(B-V) 0.1–0.4 (only 82/996 below 0.1) while reference rows have median E(B-V) 0.08 (95 % = 0.25). At matched G 15–16 & E(B-V): real vs copies pass 0–0.1: 0.82/0.96, 0.1–0.2: 0.60/0.95, 0.2–0.3: 0.42/0.92, 0.3–0.4: 0.17/0.84 (G 14–15 similar). Per-star σ/σ_fit(G) barely differs pass vs reject (0.93 vs 0.96) → noise amplitude not the cause. Told the user: k-NN with per-bin τ cannot by itself reject in-distribution stars (copies pass 0.95 by construction), so "k-NN limit" is not an explanation; faint problem ≈ the extinction problem + a ~10-pt residual at low E(B-V) (candidates: coefficient correlations not in h5 → need Gaia archive, non-Gaussian tails, population). Note: clip box is 1–99 % (user said 5–95 %).
- **2026-10-26: k-NN improvement work PAUSED by user** (extinction mean-shift augmentation, Spec2Labels label-space gate, faint residual gap: all discussed, nothing implemented). Resume only after the XGBoost RGB-only classification reaches a break point. Current gate to use = fit-σ ref_all (results/knn/noise_augmented_fitting/ref_all, d_tgt ≤ 1). Plan for classification: see [[xgb-rgb-plan]].
- 2026-10-26: user wants NO duplicate paths: symlinks rgbrc_prelim and xp_rgb_kiel deleted; all notes (local + Notion) must use <WORK>/age_inference_from_xp_spectra/... Never leave compatibility symlinks when renaming; update references instead.
