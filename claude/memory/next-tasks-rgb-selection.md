---
name: next-tasks-rgb-selection
description: "Upcoming tasks as of 2026-10-14 — Spec2Labels pred-vs-APOGEE Teff/logg plots, then discuss how to select only RGB from unlabelled stars (main sequence, AGB regions)"
metadata:
  node_type: memory
  type: project
  originSessionId: 3d315639-11f7-4e89-9c8b-939801bbdd86
  modified: 2026-10-13T16:50:35.088Z
---

The user set the next tasks on 2026-10-13, to start on 2026-10-14.

1. **Visualisation (Spec2Labels vs APOGEE)**
   - A Kiel diagram of Spec2Labels-predicted Teff (x) against predicted logg (y), coloured by APOGEE logg. A second version is coloured by APOGEE Teff.
   - Scatter plots of predicted vs APOGEE logg and predicted vs APOGEE Teff.
   - Purpose: if the predictions are accurate, main-sequence stars could be cut by hand in predicted logg/Teff before the RGB/RC classifier runs.
   - The plan, with caveats, is in Notion daily log 2026-10-14: evaluate on testing_set (the FM was trained on training_set labels), compare XP-only vs XP+colours, and use GPU 0 with an nvidia-smi check first.
2. **Discussion**: how to pull out only RGB stars from unlabelled data whose evolutionary state is unknown. This includes regions that are probably main sequence or AGB, which the 2-class RGB/RC classifier cannot reject.

**Why:** the XP-only RGB/RC classifier labels main-sequence and subgiant stars as RGB, even at a p ≥ 0.95 threshold (93 % stay RGB). Its "RGB" really means "not RC-like". See [[xp-rgb-kiel-results]].

**How to apply:** when resuming, start from these two tasks.
- For the discussion, options already on the table are: a predicted-logg/Teff cut, a 3-class RGB/RC/other classifier, and extinction handling.
- AGB labels are scarce: only 22 stars in the Kepler evstate file ([[apogee-kepler-evstate-dataset]]).
- Record outputs under age_inference_from_xp_spectra/ (not in the repro repo), and write them to Notion following [[notion-writing-rules]].

**Task 1 done (2026-10-14)** in age_inference_from_xp_spectra/preliminary/spec2labels_vs_apogee/ (run.py, contam.py; predictions with source_id in predictions_testing_set.npz). Results are in the 2026-10-14 Notion daily log, toggle 今日の結果.
- testing_set, 25,193 stars. XP+colours: 47.2 K / 0.111 / 0.075. XP-only: 51.2 K / 0.121 / 0.080.
- Cut at predicted logg < 3.5: 2.4 % (XP+colours) / 3.3 % (XP-only) of dwarfs leak into the giant side; about 1 % of giants are lost; 1.2–1.6 % of the selected sample are dwarfs.
- The gross outliers (APOGEE logg > 4.2, predicted < 3.0) have a large model logg_error (median 0.7–0.8 dex), so an uncertainty cut should remove them.
- Open oddity: in the predicted Kiel diagram, the upper RGB (logg < 1) splits into two bands.
- Next: refit the RGB/RC classifier after the cut; check leakage as a function of E(B-V); then task 2, the discussion.
- Reliability check (reliability.py/.log): the cut is reliable only at low extinction and bright magnitudes.
  - Dwarf leak (XP-only / XP+colours): E(B-V) < 0.1 → 1.2 / 1.0 %; 0.1–0.3 → 10 / 7 %; 0.3–0.6 → 24 / 13 %; > 0.6 → 42 / 28 %.
  - By G magnitude: G > 15 → 12 / 8 %.
  - The overall 1–2 % contamination only looks small because testing_set has few dwarfs at high extinction.
  - Excluding the 642 stars that are also in training_set changes nothing.
  - Added to the Notion page in the section 信頼できる範囲.
- User's direction (2026-10-14): a classifier that picks RGB alone is preferred if feasible; a manual cut that removes main-sequence stars is acceptable.
  - I proposed a 2-stage approach: a predicted-logg cut, then the RGB/RC classifier. AGB is handled by restricting the logg range.
  - The key issue is extinction handling, not "cut vs classifier".
- 2026-10-14 discussion on detecting stars unlike the training data (option C: out-of-distribution, OOD).
  - Written under the 2026-10-14 daily log toggle "meeting at 12:00で話すこと" → 対策 → 見たことがない星を検出する. The user wrote that toggle themselves; I filled in the empty child bullet.
  - The user will discuss it with their boss.
  - Proposed first step: a k-NN distance computed in two spaces (XP coefficients and predicted labels).
    - Metrics: AUROC for in-training-range stars (Kepler RGB/RC) vs stars outside it (dwarfs, E(B-V) > 0.3 giants), plus an accuracy-vs-coverage curve.
    - Add the foundation-model embedding as a third space later.
  - Wait for the boss's feedback before running it.

**Update 2026-10-14 (later):** Option C plan fixed — input XP (+colours if needed); main = k-NN distance in FM encoder latent (`_perception_memory` after perceive(), per-token → pool, e.g. mean over non-padding); comparisons = raw XP distance and Spec2Labels-predicted Teff/logg distance (user stressed 1 vs 3 differ because 3 passes the decoder). Ground truth = human visual inspection of Kiel plots for now; automatic in/out definition (KDE 95 % contour of fits stars) is on hold, kept in ideas.md 5.3. Fits-alone Kiel plots: age_inference_from_xp_spectra/apokepler_evstate_kiel/.

**Task queue (decided 2026-10-14, not started; user asked to keep as tasks):** k-NN OOD gate. Spaces: 1a = encoder output mean over the 64 tokens (128-d), 1b = flatten 64×128 → PCA (~128; fit PCA on reference + random target sample); comparisons 2 = raw xp_coeffs_gnorm 64, 3 = Spec2Labels Teff/logg(/[M/H]). k≈10, standardize by reference stats, τ = 95th pct of reference leave-self-out d_k. Ground truth = visual Kiel plots.
1. Reference = RGB+RC+AGB (design A, 4,775 fits stars with XP; user insisted AGB included) → compare 1a vs 1b, pick one.
2. Same reference: add spaces 2 and 3.
3. Reference = RGB only (design B, 2,670) vs A; check RGB-vs-RC AUROC of d_k.
Plan page in Notion: 3f937d5c3621817ba40bca1307fd833e (under Research, right after the Spec2Labels page).

**Update 2026-10-15:** task 1 of the k-NN queue is done (use 1a). The current state and next steps are in [[knn-ood-status]].
