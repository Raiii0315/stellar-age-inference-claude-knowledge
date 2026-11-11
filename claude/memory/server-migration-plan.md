---
name: server-migration-plan
description: "2026-11-11: moving fully to a new server (current one will go away); knowledge carried via private repo stellar-age-inference-claude-knowledge; no sh scripts, step-by-step only; outputs re-run not carried; final public pipeline repo after age inference"
metadata:
  node_type: memory
  type: project
  originSessionId: c9ad5d19-b14a-4a2c-832d-a746f6ff5e3f
  modified: 2026-11-11T17:15:12.928Z
---

2026-11-11: the user will move completely to a new server (not in parallel); the current server will basically become unavailable. Not moving yet; preparing now.

New server layout: one parent dir `<WORK>` (e.g. an "inference stellar ages project" dir) holding `age_inference_from_xp_spectra/`, `astroNN_stars_foundation/`, `astro_data/`, `stellar-age-inference-claude-knowledge/`. Claude will be launched in `age_inference_from_xp_spectra` there. SSH (server + GitHub), uv and Claude Code are already set up on the new server. Do not run `/init` there.

Knowledge transport: private repo `git@github.com:Raiii0315/stellar-age-inference-claude-knowledge.git`, local clone at `<WORK>/stellar-age-inference-claude-knowledge/`. Holds `claude/` (CLAUDE.md, CLAUDE.local.md, settings.local.json, memory/) + README + setup/. `server-current.md` is gitignored (each server writes its own). Memory updates on this server are committed with the fixed message "Update memory" (user approved).

User decisions:
- **No sh scripts.** The user wants to do each step by hand, knowing what and why — never bundle steps into a script that "does it on its own". Install/sync scripts were deleted.
- **Data:** the evstate fits is already on the user's PC (they scp it). Zenodo h5 and APOGEE allStar are re-downloaded; `*_abund.h5` is regenerated.
- **Analysis outputs (npz) are NOT carried.** Repro (foundation model) outputs stay on this server only. Only outputs that age inference really needs are re-run on the new server; I am to trace which ones and in what order.
- **Re-run chain for age inference (traced 2026-11-11, read-only):** data = Zenodo training/testing_set.h5 + evstate fits (allStar and *_abund.h5 NOT needed for age; [α/M] comes from the fits). (1) `train_set_match_parameters/match_kepler_to_h5.py` → `train_set_match_parameters/out/match_*.npy` (`paths.MATCH_DIR`; moved from preliminary/00_early_checks/a2.py on 2026-11-11, output identical; h5 has a few duplicate gaia_source_id rows, the last row is used — user: not worth examining, do not mention in code/README) → (2) knn_ood/g_band_correction_augmentation/08_sigma_vs_g_coeffs.py → (3) 09_sigma_fit.py (sigma_fit_poly3.npz) → (4) age_estimation/augmentation_using_age_and_G_band/scripts/age_augmentation.py (seed 20261106) → (5) age_estimation/data_split/scripts/make_split.py (seed 20261110) → (6) make_fold_data.py (fold0–4.npz). G-band augmentation outputs (two_per_row etc.) are figures only; RGB classifier / k-NN outputs are needed only when applying the age model to Gaia stars.
- **No sha256 checks** — if results differ a lot, the user will investigate the cause then.

After the move (handoff.md §7, user 2026-11-11): once Claude reads memory/rules on the new server and handoff §5 is done, delete the local clone of stellar-age-inference-claude-knowledge on the new server (ask first); the user decides at move time whether to Archive or delete the GitHub repo; then rewrite this memory as "migration done".

Future plan (a note, not a task now): after age inference is fully done, the user will make one more repo with only the needed pipeline and make it public. Everything in that public repo (scripts, comments, READMEs) will be in English (user, 2026-11-11). The current repos stay in Japanese until then.

See [[no-server-paths-public]], [[user-writes-commit-messages]], [[agree-before-running]].
