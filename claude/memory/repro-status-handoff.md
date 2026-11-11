---
name: repro-status-handoff
description: "Leung & Bovy 2023 reproduction status as of 2026-10-09 — what is done, open decisions, run conventions; read first when resuming"
metadata:
  node_type: memory
  type: project
  originSessionId: d12aff24-e5b6-43a3-abcc-353fda85484f
  modified: 2026-10-09T09:28:29.358Z
---

Status on 2026-10-09 (session handoff). Details live in `repro_notes/results.md`, `deviations.md` and `ideas.md`.

**Done.** All of the following reproduce the original notebook outputs, using the bundled pretrained `model_torch/` with no retraining:
- Spec2Spec (run1)
- Spec2Labels (run1): 47.20 K / 0.11 / 0.07
- Labels2Labels (run2)
- Labels2Spec (run2): NN values identical
- Task_TopKSearch (run2): identical top-10 stars

**Open — the user still has to decide:**
1. **Inference_ExternalComparison is not started** (user said 2026-10-09: not now — on hold). It needs Gaia DR3 XP for 16.76M Andrae et al. 2023 stars. The full XP mirror is about 3.6 TB, which did not fit on the first server's storage (about 436 GB free); recheck on the current server. Options I presented:
   - A: stream the 3386 CDN files and keep only the needed rows (mygaiadb format)
   - B: Gaia archive DataLink
   - C: random ~1 % subsample (my recommendation first)
   The author's result to match is σ 55.91 K / 0.18 / 0.10. Inputs are BP/RP[0-31] only. Andrae source_ids are cached in the old session scratchpad, which may be gone; regenerating takes ~1 min from the fits.
2. **Rename `figs/*.pdf` to paper figure numbers** (e.g. `fig03_...`). The figure numbers have not been checked against the paper yet.
3. ~~Commit~~ Done 2026-10-09: commit e103e24 pushed to private repo git@github.com:Raiii0315/astroNN_stars_foundation_repro.git (`origin`; henrysky is `upstream`). SSH key ~/.ssh/id_ed25519 registered. Repo-local git user Raiii0315 / miyarai.0315@gmail.com. figs/ and .python-version are force-added (gitignored upstream); andae catalog, data_files, Zhang model excluded. Commit/push further changes only when asked.

**Run conventions:**
- `uv run python repro_runs/make_run_copy.py <nb> runN`, then `CUDA_VISIBLE_DEVICES=<allowed GPU> uv run python repro_runs/run_notebook.py repro_runs/<nb>_runN.ipynb > ...log 2>&1`.
- Delete failed-run files once the cause is recorded in `deviations.md`/`results.md` (the user approved this).
- See [[confirm-gpu-before-run]] and [[no-latex-repro-only]].

**Environment facts learned:**
- astroNN is pinned to GitHub commit 04d79916, because PyPI 1.1.0 lacks the Gaia G band.
- Whether TF can use the GPU depends on the server's driver (on the first server the driver supported only CUDA 12.4, so TF 2.20 could not use it and the run copies hid the GPU from TF).
- `model_torch_search.pt` needs a visible GPU to load.
- The original notebooks were run by the author on Windows, i.e. not in Docker.

**Notion:** see [[notion-writing-rules]].

**XP → RGB/RC research: the 2026-10-09 (2nd session) preliminary work is done; the next session starts concrete discussion.**
- Label data: [[apogee-kepler-evstate-dataset]]. It is summarized in Notion データセットについて, chapter 3.
- Prior work found (abstracts only): Yu & Chen 2026 arXiv:2608.28522 (XP → ΔΠ1/Δν with an MDN; RC catalog of 532k stars at 97 % purity) and Barman+2026 arXiv:2604.17045 (A&A; CNN-LSTM, XP → Δν/νmax/ΔΠ1). So "can XP separate RC?" is no longer novel.
- Proposed framing (not decided): evaluate the foundation model on this task. Frozen-encoder linear probe, a label-efficiency curve, a control using the model's predicted Teff/logg/[M/H] only, and testing_set stars held out.
- Verified: Hawkins+2018 (APOGEE, The Cannon, CN/CO/[C/N]) and Ting+2018 (LAMOST, ~3 % contamination).
- Correction (already reflected in ideas.md): M_K/brightness adds little at fixed Teff/logg; at APOGEE precision, Teff/logg already separate RC from RGB ~94 %.
- 論点2 done (age_inference_from_xp_spectra/a6.py, a6.log; summary in ideas.md): Kiel+XP 0.962 vs Kiel 0.938 (paired, significant); XP adds nothing once [C/N] given; XP-only 0.941. Not committed.
- Target clarified 2026-10-13: user wants RGB/RC for training_set stars WITHOUT evstate (not stars without APOGEE values). a7/a8 (age_inference_from_xp_spectra): 91k target giants have allStar [C/N]; Kiel+[C/N] stable vs extinction, XP-only classifier collapses at E(B-V)>0.3. allStar DR17 is local at astro_data/sdss_mirror/dr17/apogee/spectro/aspcap/dr17/synspec_rev1/. Proposed: Kiel+[C/N] silver labels -> train/evaluate XP. Not in Notion yet.
- 2026-10-13: read full text of Yu & Chen 2026 and Barman+2026 (PDFs were in the session scratchpad, may be gone; summary in ideas.md). Key: Yu & Chen already did seismic->APOGEE/LAMOST (Ting+2018) silver labels -> XP; Barman validated only within Kepler (Vrard+2025: 84%). Vrard+2025 = 18,784 Kepler evstates, a bigger label set than ours.
- No new repo created yet; its location is still undecided. Preliminary scripts and the figure are in <WORK>/age_inference_from_xp_spectra/ (see its README.md). ideas.md committed and pushed as c4cda4e.

**Discussion topics to carry over:**
- The training set has no giant/dwarf labels; about 1/3 of stars lack APOGEE labels.
- XP-based RGB vs red-clump classification is noted as an idea, not to implement here.
- Questions for Prof. Kawata (10-09) are in the 10-08 daily log toggle 「検討」.
- The literature I cited there (APOKASC, Hawkins+2018, Ting+2018, Bovy+2014) is unverified.

- 2026-10-13: ideas.md reorganized (goal = RGB ages; step 1 = RGB/RC via foundation-model representation; open question "is FM needed vs XGBoost" decided by OOD robustness / label efficiency). Committed 9b8cee7. Notion 研究進捗（13/10/2026） updated with same structure. Next candidate: embedding linear probe vs XGBoost (needs GPU 0 approval).
- 2026-10-14: ideas.md gained chapter 5 (RGB-only selection: why the 2-class classifier labels dwarfs as RGB; options A cut / B 3-class / C OOD detection) and Spec2Labels cut results in chapter 2. Old chapters 5 and 6 are now 6 and 7. Committed and pushed as dd93d7d (2026-10-14, OOD plan; prev ec4b1bf).
