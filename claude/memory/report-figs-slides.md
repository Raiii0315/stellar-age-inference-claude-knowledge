---
name: report-figs-slides
description: "Space Coffee 2026-11-11 talk figs + slides all in age_inference_from_xp_spectra/report/space_coffee_2026_11_11/ (same name for script/fig/log); edit report copies only"
metadata:
  node_type: memory
  type: project
  originSessionId: 5c8589d5-ac00-4ccd-b1b2-84d7f0cebdf9
  modified: 2026-11-11T00:00:00.000Z
---

2026-11-10: the user is making English slides for a progress talk (RGB classification only; age inference = next steps; acknowledgements required at the start or end).

2026-11-11: all slide material was consolidated into ONE folder, `age_inference_from_xp_spectra/report/space_coffee_2026_11_11/` with `scripts/`, `figs/`, `logs/`, `slides/`.
- Naming: script, fig and log share one name (`kiel_selection_compare.py` → `figs/kiel_selection_compare.png`, `logs/kiel_selection_compare.log`); no number prefixes (user: correspondence is enough).
- The old per-analysis `report/` subdirs (rgb_classifier/{testing_set,xgboost_analysis}/{scripts,figs,logs}/report, knn_ood/report) were removed after the re-run figures matched pixel for pixel.
- The old `<WORK>/slides/` folder was deleted (user approved). Slides the user scp's later go in `report/space_coffee_2026_11_11/slides/` and are committed to git.
- Future talks: make a new sibling folder `report/<talk>_<yyyy_mm_dd>/` with the same layout.

**Rule (user, 2026-11-10):** change only the report versions. Leave the original figs and scripts untouched. When a figure already exists, copy it into `report/` instead of moving it.

**Why:** the original analysis outputs are cited in Notion and in READMEs.

**How to apply:** make new report scripts and leave the originals alone.

Decisions about which spec2label cut to use:
- The panel "spec2label cut → XGBoost" uses the old cut: predicted Teff 4000–5500 K AND predicted logg 1–3.5. This is the same range as Ciucă et al. 2024.
- The panel "k-NN → cut" uses the logg-only cut.
- The user chose not to align the two cuts.

Slide text (2026-11-10): when making slides, always write the speaker manuscript in the slide notes (English talk).

Motivation slide decisions:
- M1a uses "What each star can tell us": chemistry (for most elements) / dynamics (present-day orbit) / age. Ages are the missing piece. No "fossil" analogy, because C and N change in giants and orbits change over time.
- The Almannaei et al. slide stays short: "ages can be inferred from stellar parameters and abundances".
- Never frame this work as "improving Aisha's result". Frame it as the step needed before age inference.

See [[agree-before-running]] and [[change-only-what-asked]].
