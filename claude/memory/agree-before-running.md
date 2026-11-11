---
name: agree-before-running
description: "Before starting any analysis task, agree on directory structure, file names and task order with the user and record them; never start running just because the order was decided"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 2afffb71-5273-473a-b436-a49f49922427
  modified: 2026-10-27T13:02:47.239Z
---

Agreeing on the order of tasks is not permission to start them. Before writing scripts or running anything for a new task, first propose the directory structure, script and figure names, and the steps. Then record them (memory, and Notion if asked) and wait for the user's go-ahead.

**Why:** on 2026-10-27, after the user fixed the order (1 → 3 → 2 → 4 → 5), I immediately wrote rgb_classifier/08_knn_pass_logg.py and ran it. The user said 「まだ勝手にやらないでください．ディレクトリ構成ややることの順番をメモしていません」. The same thing happened on 2026-10-26 (「まだ実行しないで．ディレクトリ構成や閾値など決めることがまだありますよね」).

**How to apply:** for each item of [[xgb-rgb-analysis-plan]], propose the dir/files/steps → the user approves → record → only then implement. Related: [[change-only-what-asked]].
