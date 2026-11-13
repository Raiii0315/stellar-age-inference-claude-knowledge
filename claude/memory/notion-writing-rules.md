---
name: notion-writing-rules
description: "Where and how to write this project's notes in the user's Notion (海外インターン pages); writes are pre-approved"
metadata:
  node_type: memory
  type: feedback
  originSessionId: d12aff24-e5b6-43a3-abcc-353fda85484f
  modified: 2026-10-20T11:19:02.369Z
---

The user keeps research notes in Notion under 海外インターン, and writes there are pre-approved: 「承認するので確認は入りません」.

Rules:
- Do not just append. Insert content into the appropriate existing section and restructure the page if needed. The user asked for this repeatedly.
- When updating a section with new understanding (e.g. a task toggle), rewrite that whole section as one coherent summary rather than sprinkling insertions into it (2026-10-09: user said 「差し込むのではなくもう一度まとめ直して」 after insertions left the TopKSearch toggle with duplicated subsections).
- Write in Japanese, plainly, for someone new to the project.

**Structure (reorganised 2026-10-20 at the user's request).** Page ids did not change, so the per-page ids below are still valid, but the parents changed. Top level of Research at UCL MSSL, UK (3cf37d5c36218087a8d5f7aa6a4b7c7a): 研究テーマ案, 研究方針, 研究ログ (holds デイリーログ), 研究進捗, 基礎知識・資料, 先行研究・再現, 論文AI要約プロンプト, 研究環境・事務メモ.
- 研究進捗 (3ff37d5c362181db98b9f4e235b5fd9f) holds the 08/10 and 13/10 snapshots and three theme pages. Each theme page has a short 目的 / 分かったこと summary at the top, then its analysis pages in date order:
  - XP から RGB / RC を判定する予備実験 (3ff37d5c3621818e898cc427719cb2b7): XP-only Kiel, Spec2Labels.
  - k 近傍の距離による判定 (3ff37d5c362181b9bdb1c93d204e0a2a): plan, task 1, pass/reject, RGB-only, axis clipping.
  - G バンド補正 (3ff37d5c36218104b5eefa4875cf0986): policy, Step 1, Step 2.
- 基礎知識・資料 (3ff37d5c362181b19c16f7c6d661f88e): データセットについて, Evolution of stars, Gaia BP/RP 基礎メモ, and three general-basics pages from the 10-20 catch-up: flux と等級 (3ff37d5c362181ef9914cf54d52a07dc), Gaia XP スペクトルと XP 係数 (3ff37d5c362181a29a94ee025e42fbbd), 測定のノイズと誤差 σ (3ff37d5c362181e9a058cc021be8a790). Each page: 目的 / body / 確認の問いと答え / 次のページ. The user wants general basics kept separate from experiment and implementation content (pipeline steps, this research's numbers, k-NN). That content goes in the research pages. On 2026-10-20 the overlapping parts of the G-band policy page and the Step 1 page were replaced with links to these pages.
  - The flux page holds my catch-up stage 1 explanation. The user found it clearer than the old §4 of the Gaia BP/RP memo, so on request I deleted that §4 and renumbered §5–6 → §4–5.
  - During catch-up, show explanations in chat only. The user said 「notionに勝手にメモせず」: write to Notion only when asked.
- 先行研究・再現 (3ff37d5c362181f3b7d6c0fd2e93a21d): 先行研究, astroNN 再現. The user wanted these two kept separate from 基礎知識.
- 研究環境・事務メモ (3ff37d5c362181e4ada1eb96c2a13c21): GPU, hot desk and uv sync notes.
- **New analysis pages:** create them under the matching theme page, not under Research, and update that theme page's summary. Start a new theme page when a new topic starts.

Pages:
- **Progress table and next steps:** page 研究進捗（08/10/2026）, id 3f337d5c362180fea17debf3d960cc67.
- **Progress 2026-10-13 (XP → RGB/RC findings):** page 研究進捗（13/10/2026）, id 3f837d5c3621815d9426c201ca931808, placed right after the 08/10 page under Research at UCL MSSL, UK (parent id 3cf37d5c36218087a8d5f7aa6a4b7c7a). New progress snapshots get their own dated page there; I read "直下に" as sibling right below (not child); not yet confirmed by the user.
- **XP-only RGB/Kiel task write-up (2026-10-13):** page XP のみで RGB を判定し Kiel 図で確かめる（13/10/2026）, id 3f837d5c3621810c8d45db04690baae7, child of Research at UCL MSSL, UK. Figures were uploaded via create-file-upload + curl POST, which works for local images.
- **Spec2Labels vs APOGEE write-up (2026-10-14):** page Spec2Labels の予測 Teff・logg と APOGEE の比較（14/10/2026）, id 3f837d5c3621801ca4bac0490ded29a6, child of Research at UCL MSSL, UK. It was the user's blank untitled page; I titled it.
  - Structure: 共通の設定, then for each of the 4 graphs 目的 / 入力 / 図の構成 / 結果, then 分かったこと.
  - Terminology the user asked for: 「標準偏差」 (MAD-based) and 「差の中央値」 (median of d = 予測 − APOGEE). Do not use ばらつき/偏り.
- **Daily explanations:** 研究ログ / デイリーログ / <date>.
  - On 2026-10-08 (id 3f237d5c3621800f8519ead5b0709a53), the toggle about my research → Foundation Model のタスクについて holds explanations of each task.
  - The toggle 「検討（河田教授に10-09に質問）」 holds the RGB/RC discussion.
- **Consolidated astroNN page (main reference, created 2026-10-09):** page astroNN_stars_foundation 再現（Leung & Bovy 2023）, id 3f437d5c3621815c84cce918b9c5eee9, placed right below データセットについて under 海外インターン. It merges the 10-06 and 10-08 astroNN content: overview, method, a per-task toggle for each notebook (TopKSearch includes the contrastive-loss explanation), and environment setup. Durable astroNN content goes here; the daily logs only get that day's notes.
- **Dataset discussion:** page データセットについて, id 3f337d5c36218098a267f6c8b86b5f3c. It covers training data contents, per-quantity sources, missing labels, the RGB question, and the comparison with Andrae.
- 2026-10-06 daily log (id 3f137d5c362180389adbd14380a6e97e) has the environment setup and method explanation.
- 2026-10-13 daily log (id 3f837d5c362180159cedf260a3f58ebc): user-made toggles "space coffee" (paper summary, not mine) and "about my Research" (my corrected summary of the day: goal, XGBoost-inside-Kepler, silver labels vs Spec2Labels, Almannaei/FS24, correction table, a section 今日の実験 summarising the XP-only RGB/Kiel experiment with a link to its own page, and next steps).
- 2026-10-14 daily log (id 3f837d5c36218066a08aeeca5845d063): toggle 今日やること holds the plan to compare Spec2Labels-predicted Teff/logg with APOGEE. The goal is to cut out main-sequence stars before the RGB/RC classifier; evaluate on testing_set and compare XP-only vs XP+colours.
- 2026-10-09 daily log (id 3f437d5c362180e1bac6d765776a5aaf): toggles 再現作業 / TopKSearch の理解 (that day's learning points; full text in the astroNN page) / 派生研究の予備検討 (incl. a7 results, which are not yet in データセットについて) / 次にやること.

**Why:** the user is new to this field and rereads these pages to learn; scattered appends make them hard to follow.

- Uploaded images (notion-file-block://) cannot be moved or copied by rewriting Markdown; the image is silently dropped. To restructure a toggle that holds an image, edit the text around the image in place (2026-10-13). Image blocks also cannot be matched by update_content's old_str. To swap one image, run replace_content on the whole page: keep the other images' notion-file-block:// refs as fetched (they survive, verified 2026-10-13), and give the new image a fresh file-upload:// source. Use a versioned filename (e.g. _v2) so the Notion cache shows the new image.
- 2026-10-13 daily log also has a top-level toggle training_set_kiel (the user's own image plus my explanation of age_inference_from_xp_spectra/a12_kiel.py), placed below about my Research.

**How to apply:** after finishing a task or discussion the user wants recorded, fetch the target page first, then use update_content to place the content in the right section. Verify by re-fetching after structural edits.
- **k-NN OOD gate (2026-10-14):** plan page 学習していない星を距離で見分ける方法（計画、14/10/2026）, id 3f937d5c3621817ba40bca1307fd833e; results page k 近傍の距離による判定：タスク 1（1a 平均と 1b PCA）の結果（14/10/2026） (1a and 1b both written up 2026-10-15; plan page task 1 checked), id 3f937d5c36218100887cf0815a8ff14d. Both under Research, right after the Spec2Labels page. User wants each figure/table to say how to read it (見方) and the result (結果) explicitly.
- 2026-10-16 daily log (id 3ff37d5c362181698c27fc521fe396de) and 2026-10-19 daily log (id 3ff37d5c36218142a071d71b3578ede6): written retroactively on 2026-10-20 at the user's request. Each has toggles 今日やること / 今日の結果 (summary + mention links to that day's result pages, then 次にやること). Daily-log DB rows: set タイトル, 日付, 作業カテゴリ, 一言まとめ.
- 2026-10-20 daily log (id 3ff37d5c3621813abd6fd766596f4c32): toggle 今日やること = (1) catch up on the 10-19 G-band work, which the user did not understand at the time; (2) the boss's suggested plot, x = c, y = f/f_G, to see the effects of variance and bias. The meaning of c and f and the reason for the plot are still unconfirmed.
- 2026-10-15 daily log: id 3fa37d5c362180ebb4c4ce1c340761f1. It has the user's Space Coffee toggle and my toggle 今日やること (the k-NN 1a pass/reject validation plan). That toggle filled an empty toggle the user had left.
- **k-NN 1a pass/reject analysis page (2026-10-15):** k 近傍の判定（1a 平均）の pass / reject の解析（15/10/2026）, id 3fa37d5c362181289df6c32449598699, child of Research at UCL MSSL, UK.
  - Structure: 目的, 共通の設定, then one section per axis, each with やったこと / 図 / 図の見方 / 結果 / 言えること, then a combined summary.
  - The last section, 他の軸での解析（これから追加）, is the slot for future axes (CMD, ΔTeff, [M/H], l/b). Add new axes as new sections before it and update the combined summary.
  - The user asked to keep this page to figure results only. The classifier-check (04) and validation-method discussions were deliberately left out.
- **2026-10-16 pages (children of Research at UCL MSSL, placed right after the 15/10 pass/reject page):** 参照を RGB だけにした k 近傍の判定（16/10/2026）, id 3fb37d5c362181408f2cd66223bd2d95; 軸を増やしながら参照の範囲に絞り込む解析（16/10/2026）, id 3fb37d5c362181c79de7ed4a090f3e06. Structure: 目的 / 概要 / 設定 / 結果 / 図ごとに構成・見方・結果 / 言えること. User's stated purpose for these: find what k-NN pass/reject is based on, i.e. which parameters/abundances XP carries after the encoder, as a step toward extracting RGB. New child pages land at the end of the parent; move the <page> tag with update_content (remove + re-add in one call works).
- **2026-10-19 page:** k 近傍の判定が G 等級で変わる理由と G バンド補正の方針（19/10/2026）, id 3fe37d5c362181d381d7fc434d87f17d, child of Research, placed right after the 16/10 axis-clipping page. Sections: 目的 / 結論 / 1 XP 前処理（①÷G flux ②64 係数 ③標準化, example table）/ 2 parameter-matched pass by G / 3 err÷spread / 4 G histogram (image) / 5 augmentation plan / 次にやること / ファイル. Links to the user's own page Gaia BP/RP（XP スペクトル）基礎メモ（19/10/2026）, id 3fe37d5c362181888ec0fd0f727b3838 (basics: flux, magnitude, normalisation). User did not know "flux" — explain basic terms plainly.
- 2026-10-19: added §3 BP−RP（色指数）とは to the user's Gaia BP/RP 基礎メモ page (3fe37d5c362181888ec0fd0f727b3838); old §3–5 renumbered to §4–6. Numbers there were measured from training_set (Teff→BP−RP table, BP−RP ≈ 1.19 + 1.32 E(B−V) for 4750 K giants). GOTCHA: a table inside a Notion numbered list restarts the numbering — use "- **(1) …**" bullets instead.
- **Plan pages vs result pages (2026-10-20):** results go in the Step pages (e.g. G バンド補正 Step 1 / Step 2), not in the plan page. The policy page keeps only the policy, the procedure, and links to the results. The user corrected me after I put Step 1/2 numbers into the policy page (「書くべき所はstep1, 2のページなのでは？」). Explanations of a step's method (e.g. the σ_add worked example) go on that step's page.
- **Figures need a purpose line (2026-10-20):** for every figure, add a first bullet **なぜこの図を描いたか** saying what the figure is meant to investigate or confirm, before 図が表すもの / 見方 / 結果. The user asked for this explicitly. When I explain a step more clearly in chat, the user wants that step's own page rewritten with the explanation, not a new page.
- **2026-10-23:** page noiseを入れる前後での比較（23/10/2026）, id 40237d5c362181b98f0fc82cfcdc510f, child of the G バンド補正 theme page (user said 「研究進捗に」; put under the theme page per the rule above). Holds pass_vs_g.png (3 columns: (a) giants Teff/logg, (b) + E(B-V)<0.1, (c) all 7 labels in ref 1–99 %) with the user's own wording of purpose and conclusion. The theme page's 今の状況 was updated. The user writes 「表」 for a figure.
- 2026-10-23 daily log: id 40237d5c36218159bbf0ee9140e9e66f (toggles 今日やること / 今日の結果 with 次にやること). 2026-10-26 daily log: id 40237d5c36218119bb5dd5e31d3a0d7a (toggles 今日やること / 今日の結果 with links to the remaining-issues, method and results pages, directory tidy-up, 次にやること). 2026-10-27 daily log: id 40637d5c362181839828d994ab3041be (今日やること agenda + 今日の結果 toggle + user's Space Coffee). 2026-10-28 daily log: id 40637d5c3621813ab878fdc42679e6ea (今日やること = item-2 review first, then 4→5→6). XGBoost results page: XP から RGB を分類：結果（26/10/2026）, id 40537d5c3621813386fbd0febb44d2e6, under 研究進捗.
- 2026-10-26: user-made pages directly under 研究進捗 (not under a theme page): Gバンドのノイズを入れた後も残る課題（26/10/2026） id 40537d5c3621802c8dfeef5437e90d3c (actually under G バンド補正), and Gバンド補正後のXPからRGBのみを分類する（26/10/2026） id 40537d5c3621806ebb62c4f0f04e59ee (I wrote 目的 / 予備実験の結果について / 前提 / 先に決めること / これから行うこと). Use <mention url="..."/> (not <mention-page>).
- **Daily-log titles (2026-11-02):** write the weekday in brackets after the date, e.g. タイトル "2026-10-30 (Fri)" (user's request). 2026-10-29 log id 40837d5c3621816799b7d3ebcb5f4969; 2026-10-30 log id 40c37d5c36218146b9c9df107b6489e5 (written retroactively on 11-02). All existing titles (10-05 … 10-29) renamed with weekdays on 11-02. 2026-11-02 log id 40c37d5c36218024bd41e4afb96b2970 (was the user's blank page; filled with 今日やること / 今日の結果 + 次にやること).
- 2026-11-04 (Wed) daily log id 40e37d5c3621819ebb9ee33f6d0dd82c (今日やること / 今日の結果 + 次にやること). Use <toggle> (not <details>) in Notion markdown.
- 2026-11-05 (Thu) daily log id 40f37d5c3621819bb954e8cd13340178 (user-made page with Space Coffee; I added 今日やること / 今日の結果 + 次にやること toggles ABOVE it, set 一言まとめ and 作業カテゴリ).
- 2026-11-06 (Fri) daily log id 41037d5c3621819bb6fbe70a3c90db10; 2026-11-09 (Mon) daily log id 41337d5c3621811f9cfffa3dc267a5a4. Daily-log DB data source: collection://26b8712a-85f0-4e60-9565-b12344bdbcae (props タイトル, date:日付, 作業カテゴリ, 一言まとめ, 状態, 作業時間(h)).
- 2026-11-10 (Tue) daily log id 41437d5c362181f7880ceb63a0045133 (user Space Coffee toggle; I added 今日やること / 今日の結果 above it on 11-11: slide structure, report figs, slide text). 2026-11-11 (Wed) daily log id 41537d5c362181f7b3f4d66fb90be623 (talk, k-NN distance check, server-move prep, decoder 作り方 3, commits, 次にやること).
- 2026-11-12 (Thu) daily log id 41637d5c362181c1bd95de3e88dbf6f5 (hyperparams, query, loss/σ_y, training settings, 次にやること).
