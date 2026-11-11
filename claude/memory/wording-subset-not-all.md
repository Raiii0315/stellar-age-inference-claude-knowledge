---
name: wording-subset-not-all
description: "Describe overlaps as 'a part of X (N stars) is contained in Y', never 'all N of X's ... are in Y' — すべて reads as if all of X is in Y"
metadata:
  type: feedback
---

When describing set overlaps (e.g. testing_set vs reference), write 「testing_set の一部（524 星）は reference に含まれている」. Do not write 「testing_set の Kepler の星 524 個は、すべて reference に入っている」.

**Why:** 2026-10-30 the user said the word すべて makes it read as if every testing_set star were in reference; confusing.

**How to apply:** in chat, Notion and READMEs, phrase overlaps as "X の一部（N 星）は Y に含まれている"; avoid すべて/all for subset statements. Related: [[term-main-sequence-hedged]].
