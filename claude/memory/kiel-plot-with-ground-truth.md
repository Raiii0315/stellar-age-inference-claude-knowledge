---
name: kiel-plot-with-ground-truth
description: Predicted Teff/logg Kiel diagrams get an APOGEE ground-truth panel coloured by star density, not by Teff/logg
metadata:
  type: feedback
---

When I make a Kiel diagram (Teff on x, logg on y) of predicted values, put the APOGEE Teff vs logg ground-truth panel next to it.

Colour the ground-truth panel by **star density** (hexbin, log10 N). Do not colour it by APOGEE logg or Teff, because that only repeats its own axis.

Layout is one row of 3 panels:
- left: ground truth, coloured by density
- middle: prediction, coloured by APOGEE logg
- right: prediction, coloured by APOGEE Teff

**Why:**
- 2026-10-14: the user asked for the ground-truth panel alongside the prediction.
- They then pointed out that colouring the ground truth by its own axis quantity is pointless, and chose the density-coloured 3-panel layout.

**How to apply:** see age_inference_from_xp_spectra/preliminary/spec2labels_vs_apogee/run.py for an example. Only colour a panel by a quantity that is not already one of its axes.
