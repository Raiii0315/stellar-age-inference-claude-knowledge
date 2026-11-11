---
name: user-writes-commit-messages
description: "The user writes their own git commit messages; use theirs (no attribution lines), but fix typos/grammar and confirm the corrected text before committing"
metadata:
  node_type: memory
  type: feedback
  originSessionId: b0190cd3-6753-4b4f-a5f1-355809f38d83
  modified: 2026-11-02T16:42:11.011Z
---

From 2026-11-02 the user writes commit messages themselves: 「これからコミットするときは自分でコミットメッセージを作成するのでよろしく」. First one: "change the threshold to 0.75 (from 0.9)" (age_inference_from_xp_spectra 54e20b7).

**Why:** the user wants control over how history reads.

**How to apply:** before committing, ask the user for the message (or use the one they gave), without adding a body or Co-Authored-By lines. Since 2026-11-02: if their message has a typo or grammar error, fix it, show the corrected message and get their OK BEFORE committing (「タイポや文法ミスがあったら適切に変更…コミットする前に一度これで良いか確認する」). Still grep staged files for server paths before pushing ([[no-server-paths-public]]).
