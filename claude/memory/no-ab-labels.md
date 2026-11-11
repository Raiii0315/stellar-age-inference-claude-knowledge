---
name: no-ab-labels
description: Don't name experiments/dirs/figs with opaque labels like "design A/B"; name them by what they are (e.g. ref_rgb_only)
metadata:
  type: feedback
---

Do not use opaque letters such as "design A" or "design B" in directory names, file names, figure titles, logs or READMEs. Name things by their content, for example `ref_rgb_only` and `ref RGB+RC+AGB`.

**Why:** on 2026-10-16 the user said the A/B names were too hard to follow and unclear, and asked for `refB_rgb_only`, `kiel_B.png` and similar names to be renamed.

**How to apply:** when you add an experiment variant, put the variant itself (reference set, space, cut) into the directory and figure names, and into the in-text comparisons as well. Older plan notes still say design A/B (see [[knn-ood-status]]); translate those when you talk to the user.

2026-10-19: same for step numbers — the user disliked a results dir named `step2`. Name dirs by content (noise_table, degrade_check); numbered script prefixes (01_, 02_) are fine. Keep sibling checks at the same directory level.
