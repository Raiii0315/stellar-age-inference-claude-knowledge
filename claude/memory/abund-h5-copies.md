---
name: abund-h5-copies
description: "data_files/training_set_abund.h5 and testing_set_abund.h5 — copies of the FM datasets with APOGEE [α/M], [C/Fe], [N/Fe], [C/N] added (2026-10-15)"
metadata:
  type: project
---

On 2026-10-15 the user asked for copies of training_set.h5 and testing_set.h5 with APOGEE [α/M] and [C/N] added. The originals must stay untouched, and the copies go in the same data_files/ directory.

- Made by `<WORK>/age_inference_from_xp_spectra/train_set_match_parameters/make_abund_h5.py`; its README documents the matching and the counts.
- New raw/ columns: alpha_m, c_fe, n_fe, c_n (each with _err), and apogee_allstar_row, the allStar-dr17-synspec_rev1 row index used to add more elements later.
- NaN wherever the original h5 teff is NaN (STARFLAG ≠ 0, or ASPCAPFLAG bit 19/23), or where the element flag is ≠ 0.
- Coverage: c_n has values for about 74 % of the rows that have APOGEE teff.
- Kepler reference stars: 1,670 of 4,312 have STARFLAG ≠ 0, so they are NaN in the h5. The Kepler fits still carries their values.

Related: [[apogee-kepler-evstate-dataset]], [[knn-ood-status]].
