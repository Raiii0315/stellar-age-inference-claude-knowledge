---
name: public-repo-attribution
description: "When age inference moves to its own public repo, understand what the FM encoder/decoder pieces need and state their origin clearly (MIT notice, LB23 citation)"
metadata:
  node_type: memory
  type: project
  originSessionId: 7bd1d9f7-a268-44b5-b2a6-fdb348bdce09
  modified: 2026-11-11T18:11:23.919Z
---

User (2026-11-11): the age model currently just uses the foundation model (StellarPerceptron, Leung & Bovy 2023) encoder and decoder class as they are. When age inference is finished and moved to a dedicated public repository, I must know exactly which parts are needed (encoder weights from model_torch/, stellarperceptron/ classes such as StellarPerceptronDecoder/NonLinearEmbedding, token vocabulary, standardisation stats, utils) and clearly state where each comes from.

Known so far: original repo henrysky/astroNN_stars_foundation is MIT ("Copyright 2023-2024 Henry Leung") → any copied/vendored code or weights must keep the copyright notice + licence text; cite Leung & Bovy 2023 (MNRAS 527, 1494) and the repo URL in README. Zenodo data (records/12738256) licence: check before redistributing. Also credit other sources used (Almannaei+2024 method, FS24, APOGEE/Kepler catalogues).

**Why:** user wants the origin of reused models/code stated properly in the public repo.
**How to apply:** when planning the public pipeline repo ([[server-migration-plan]]), first list every reused file/weight with its source and licence, propose attribution text (README section + NOTICE/LICENSE copy) and get OK. Related: [[no-server-paths-public]], [[xgb-rgb-analysis-plan]].
