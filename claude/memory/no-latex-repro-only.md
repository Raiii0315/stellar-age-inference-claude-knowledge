---
name: no-latex-repro-only
description: "Do not install TeX Live / LaTeX tooling; reproduction only needs numbers and figures, not paper-quality typesetting"
metadata:
  node_type: memory
  type: feedback
  originSessionId: d12aff24-e5b6-43a3-abcc-353fda85484f
  modified: 2026-10-06T13:31:07.612Z
---

Do not install TeX Live or any LaTeX packages (type1cm, cm-super, dvipng) for the astroNN_stars_foundation reproduction.

**Why:** The user only wants to reproduce results; LaTeX in the Dockerfile exists for paper-quality figures, which is not needed.

**How to apply:** Skip LaTeX setup steps. Caveat: every notebook calls astroNN `pylab_style(paper=True)`, which sets `text.usetex=True`, and labels use `\textit`/`\textbf` (not valid mathtext). Figures will fail without a workaround in the run copies (e.g. disable usetex and rewrite those label commands) — confirm the workaround with the user rather than installing TeX.
