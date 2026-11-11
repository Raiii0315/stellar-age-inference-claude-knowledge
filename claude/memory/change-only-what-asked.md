---
name: change-only-what-asked
description: when the user asks to change a cut/range in figures, change exactly that number in figure scripts — no extra cuts, no log/table scripts
metadata:
  type: feedback
---

When the user asks to change a selection in figures (e.g. "giants 1.5–3.5 → 1–3.5"), change only that bound, and only in the scripts that draw figures. Do not add other conditions, even ones used elsewhere (I wrongly added Teff 4000–5500). Do not touch log-only scripts or add new table scripts.

**Why:** on 2026-10-26 I "unified" the giant definition and added a Teff cut plus encoder-dependent log changes. The user found this confusing and unwanted, and everything had to be reverted.

**How to apply:** restate the exact change before editing. If another script uses a different but related cut, ask before aligning it. Related: [[knn-ood-status]]
