---
name: xp-rgb-kiel-results
description: age_inference_from_xp_spectra/preliminary/prelim_xp_rgb_kiel (2026-10-13) — XP-only RGB classifier checked on the Kiel diagram; durable result dir, key findings
metadata:
  type: project
---

The user asked for this as a lasting result, not a throwaway, so it lives in its own subdirectory `<WORK>/age_inference_from_xp_spectra/preliminary/prelim_xp_rgb_kiel/ (renamed from xp_rgb_kiel 2026-10-26; no symlink)` with scripts 01–04, out/*.npz, logs/, figs/ and a README.md that holds the method, settings and results. age_inference_from_xp_spectra is not a git repo.

Key findings:
- Inside Kepler (out-of-fold), XP-only accuracy is 0.944. Errors are not concentrated in the 2.3<logg<2.7 overlap region; they are biased by mass (heavy RGB and light RC get misclassified).
- Applied to training_set giants without evstate, it breaks down with extinction: the predicted RGB fraction is flat at about 0.63 for E(B-V)<0.2 and rises to 0.93 at >0.5. Kepler labels have a 99th-percentile E(B-V) of 0.395.
- GOTCHA: the h5 field raw/logc19 is the NATURAL log of the Combined19 E(B-V) (Dataset_Reduction.ipynb uses np.log), so use np.exp. a8.py wrongly uses 10**; this was fixed in xp_rgb_kiel on 2026-10-13, but a8.py itself is left unfixed.

Notion write-up: page id 3f837d5c3621810c8d45db04690baae7 (see [[notion-writing-rules]]).

Related: [[apogee-kepler-evstate-dataset]], [[repro-status-handoff]], [[notion-writing-rules]]
