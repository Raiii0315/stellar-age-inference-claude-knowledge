---
name: no-server-paths-public
description: "Never put the server's absolute path (the real value of <WORK> in server-current.md) into anything pushed to GitHub; use paths.py + env vars / relative defaults"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 5528b4b1-a6b7-45b4-8bad-068ea173d677
  modified: 2026-11-02T11:31:21.990Z
---

The server's absolute path (the real value of `<WORK>`, written in `server-current.md`) must not appear in files pushed to GitHub (code, README, logs). The user may share that server location later and does not want it public.

**Why:** user said (2026-11-02) 「現在のパスは今後共有するかもしれないので公開したくない」 while preparing age_inference_from_xp_spectra for a private GitHub repo shared with the introducer.

**How to apply:** in age_inference_from_xp_spectra, paths go through a root `paths.py` (sibling-dir defaults relative to the repo, override with env vars such as MY_ASTRO_DATA); mask paths in md/log as placeholders before committing; grep for that server path before every push. 2026-11-02: astroNN_stars_foundation_repro history was rewritten and force-pushed (paths → `<WORKDIR>`, CLAUDE.md removed from GitHub, kept locally via .git/info/exclude); pre-scrub bundle in <WORK>/git_backups/. Global git user.name/email is empty → set repo-local identity in every new repo, or commits leak user@hostname. age_inference_from_xp_spectra is now its own private repo (git@github.com:Raiii0315/age_inference_from_xp_spectra.git, first push 2026-11-02, repo-local identity set); data files are gitignored, logs/READMEs use `<WORKDIR>`. Original files of the upstream repo must stay unmodified (README.rst has only a prepended notice). Related: [[repro-status-handoff]]
