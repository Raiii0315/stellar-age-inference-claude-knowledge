---
name: apogee-kepler-evstate-dataset
description: "The RGB/RC label file astro_data/APOGEE_DR17_fits/APOGEEDR17x7dotS39.fits — evstate coding (0=AGB in this file), sizes, overlap with foundation-model sets"
metadata:
  node_type: memory
  type: project
  originSessionId: ed2f7530-343d-4637-8d7a-53ccf773ef7f
  modified: 2026-10-09T13:44:39.444Z
---

`<WORK>/astro_data/APOGEE_DR17_fits/APOGEEDR17x7dotS39.fits` is the label set for the XP → RGB/RC research (derived work, not the repro repo; see [[repro-status-handoff]]).
- TOPCAT inner join of allStar-dr17-synspec_rev1 with the Kepler seismic catalog `7dotS39_ALL_nocuts_extendedFMT.txt` ("Andrea", 2020; probably Miglio+2021 A&A 645 A85 — unverified). 5420 stars, all in the Kepler field.
- Seismic-side columns have a trailing comma in their names (`mass,`, `FE_H,`, `evstate`).
- **evstate in this file: 1 = RGB (3065), 2 = RC (2314), 0 = AGB (41).** Confirmed by the user 2026-10-09. The general coding is 3 = AGB, but this version has no 3s; its AGB stars are coded 0.
- Gaia source_id overlap with data_files/: training_set has 4312 of these stars, testing_set has 534, and 574 are in neither. XP is available for about 4775 of them.
- Quick 5-fold XGBoost baselines for RGB vs RC (accuracy): APOGEE Teff,logg,[M/H] 0.939; adding [C/N] 0.969; XP coefficients only 0.941.
