# handoff：新しいサーバーでの研究の再開

## 0. この文書の使い方

新しいサーバーで `setup/setup.md` の手順を終え、`age_inference_from_xp_spectra` で Claude を起動した直後に読む文書。
研究がどこまで進んでいて、何が決まっていて、次に何をするか、新しいサーバーで最初に何をするかを 1 か所にまとめた。

- 作業ルール（実行の前に提案して OK をもらう、commit メッセージはユーザーが書く、など）は `CLAUDE.local.md` にある。
- 詳しい経緯はメモリにある（索引は `MEMORY.md`）。この文書は要点だけを書く。
- 書いた時点：2026-11-11（移る前のサーバーで最後に整理したとき）。

## 1. 研究の目的と全体の流れ

目的：Gaia XP スペクトルから、赤色巨星分枝（RGB）の星の年齢を推定する。

流れ：

1. XP スペクトル → RGB の星を選ぶ（赤色巨星の中から、ヘリウム燃焼中の red clump（RC）や AGB を除く）。
2. 選んだ星について、年齢と [α/M] を推定する。

- XP スペクトルの特徴は、Leung & Bovy 2023 の foundation model の encoder を固定して使って取り出す。
- 学習のラベルは、APOGEE×Kepler の星震学の年齢（evstate の fits）。

## 2. ここまでに決まったこと

### 2-1 再現（Leung & Bovy 2023）

- 主なノートブック（Spec2Labels、Labels2Labels、Labels2Spec、Spec2Spec、TopKSearch）は再現済み。Spec2Labels の精度は Teff 47.20 K、logg 0.11 dex、[M/H] 0.07 dex で、論文と一致した。
- Inference_ExternalComparison は保留（Andrae et al. 2023 の 1,676 万星分の XP が必要で、移る前のサーバーには入りきらなかった）。
- 詳細：`astroNN_stars_foundation/repro_notes/results.md`

### 2-2 RGB の選び方

手順：k-NN gate → spec2label cut → XGBoost

- k-NN gate：encoder の出力が reference（Kepler の星）に十分近い星だけを残す（2-3）。
- spec2label cut：foundation model の predict logg が 1–3.5 の星だけを残す。
- XGBoost：XP 係数 64 個を入力にし、ノイズを入れたコピー（G' 9–17）を含めて学習したもの。p(RGB) ≥ 0.75 を RGB とする。

testing_set での結果：RGB と判定された星は 4,812 星（うち APOGEE の値がある星 3,297）。APOGEE logg ≥ 3.5 の星は 2.6 %。

### 2-3 k-NN gate

- reference：Kepler の 4,775 星（RGB・RC・AGB）と、そのノイズのコピー（XP 係数の誤差の G との関係を当てはめた σ で暗くしたもの）。
- 判定：encoder の出力（64 × 128 の平均）の k = 10 番目の近傍までの距離が、G ごとの閾値以下なら通す。
- Extinction の大きい星が落ちる問題などの改良は、保留している（RGB の分類のあと、必要になったら再開）。

### 2-4 年齢推定の学習データ

対象：evstate の fits で RGB、かつ XP がある 2,670 星。

- 分け方：星の単位で、年齢で層別した 5-fold（seed 20261110）。各回は学習 約 1,922 星、検証 約 214 星、テスト 534 星。
- augmentation は学習用の星だけに、2 段で行う。
  1. 年齢：年齢の分布が平らになるように、星ごとに g − 1 個のコピーを作り、年齢と XP を誤差に従って引き直す。
  2. G：各行に、より暗い G'（G から 17 の一様分布）のコピーを 2 つ足す（XP にノイズを足す）。
- 学習の行数は fold あたり 約 14,600〜15,300。検証・テストは、元の星に G' = 13〜17 のコピーを足したもの。
- 出力：`age_estimation/data_split/out/fold0.npz`〜`fold4.npz`
- 詳細：Notion「年齢推定のための augmentation」「年齢推定の学習・検証・テストの分け方」

## 3. 決まっていないこと

**年齢推定のモデル**

- **decoder の作り方**：encoder は固定する。候補は 3 つで、**作り方 3 から始める**（ユーザー、2026-11-11）。
  1. foundation model の decoder も固定し、年齢・[α/M] 用の新しい query だけを学習する（学習する重みは数百個）。
  2. foundation model の decoder を初期値にして、全体を学習し直す。
  3. 同じ形で幅を狭くした decoder を、最初から学習する。`stellarperceptron/layers.py` の `StellarPerceptronDecoder` を import し、`decoder_dense_num`（元は 3096）を小さくするだけで作れる（元コードは変えない）。
- **作り方 3 の幅と設定**（2026-11-12 に決める予定）。案：幅 256（約 39 万個。128 なら約 29 万、512 なら約 67 万。attention の部分の約 20 万は幅によらない）、head 16 → 8 → 4、GELU、dropout 0.1、年齢と [α/M] で decoder を共有し query を 2 つ、query の初期値はランダム。幅は 256 の 1 つで始めるか、128 / 256 / 512 を比べるかも決める。
- **学習の設定**：optimizer、学習率、epoch 数、early stopping を何で判定するか（検証用の元の星だけか、G' のコピーも含めるか）。
- **σ_y（ラベルの誤差）の扱い**：損失関数に年齢の誤差を入れるか。案：年齢は 0（年齢のコピーで誤差をすでに使っているので 2 回数えない）、[α/M] は入れる。
- **評価の方法**：テストの星（ノイズなしと G' のコピー）での精度を、G ごとに見る予定。APOKASC3 などとの比較は候補。

**学習データ**

- G バンドの augmentation の最終的な結論。今は two_per_row（1 行あたり G' のコピーを 2 つ）で学習してみて、結果を見て決める。

**保留しているもの**（今は進めない）

- k-NN gate の改良（Extinction の扱いなど）
- 再現の Inference_ExternalComparison

詳細：Notion「年齢推定の decoder の作り方の候補」「RGB の Age 推定の方針」

## 4. 次にやること

1. 新しいサーバーの準備（5 節）。
2. decoder の作り方と学習の設定を決める（3 節。先生と相談した結果も踏まえる）。
3. 決まったら、ディレクトリ・ファイル・手順を提案し、OK をもらってから学習のスクリプトを作る。GPU は、使ってよいものと空き状況を確かめてから使う。
4. 5 つの fold で学習し、テストの星で評価する。
5. 年齢のモデル（XGBoost で RGB を選ぶ → encoder → decoder に似た形 → 年齢）ができたら、RGB の分類と年齢の推定を 1 つのモデル（encoder → decoder に似た形）で同時に行えるかを考える（Notion「RGB の Age 推定の方針」の道 2）。
6. 年齢推定が完成したら、必要なパイプラインだけの公開リポジトリを作る（英語）。そのとき、再利用する foundation model のコードと重み（encoder の重み、`StellarPerceptronDecoder` などのクラス、トークンの語彙、標準化の平均・標準偏差など）を一覧にし、出所とライセンスを確かめて明記する。元リポジトリは MIT ライセンス（Copyright 2023-2024 Henry Leung）なので、コードや重みを含めるなら著作権表示とライセンス文を残し、README に Leung & Bovy 2023（MNRAS 527, 1494）と元リポジトリの URL を書く。Zenodo のデータを再配布するならそのライセンスも確かめる。手法（Almannaei et al. 2024、Fallows & Sanders 2024）やカタログの出所も書く。

## 5. 新しいサーバーで最初にやること

1 つずつ提案し、OK をもらってから行う。

1. **`server-current.md` を作る**
   - 理由：GPU・CPU・ストレージ・ホームの容量はサーバーごとに違い、使ってよい GPU や大きなファイルの置き場所がそれで決まるため。
   - 調べた結果を書く（git には乗らない）。
2. **Python の環境を作る**：`astroNN_stars_foundation` で `uv sync`
   - 理由：解析のスクリプトはすべてこの環境で動かすため（`uv run --project ../astroNN_stars_foundation python -I <スクリプト>`）。
   - astroNN は GitHub の commit 04d79916 に固定している。PyPI の 1.1.0 には Gaia の G バンドがないため。
3. **astroNN の設定**：`stellar-age-inference-claude-knowledge/setup/astroNN_config.ini` を `~/.astroNN/config.ini` に置く
   - 理由：`magicnumber = nan` にしないと、データを整えるコードが正しく動かないため。
4. **データを置く**
   - Zenodo の `training_set.h5`・`testing_set.h5`（約 1.6 GB）→ `astroNN_stars_foundation/data_files/`
     - 入手先：https://zenodo.org/records/12738256 から再ダウンロード。
   - APOGEE×Kepler の evstate の fits（約 30 MB）→ `<WORK>/astro_data/APOGEE_DR17_fits/APOGEEDR17x7dotS39.fits`
     - 入手先：ユーザーの PC から scp（非公開データなので GitHub には置かない）。
   - 置き場所は `paths.py` の既定（リポジトリと同じ階層）なので、環境変数の設定は不要。
5. **解析の出力を作り直す**（`age_inference_from_xp_spectra` の直下で、この順に実行する）
   1. `train_set_match_parameters/match_kepler_to_h5.py`
      - fits の各行 → h5 の行番号の対応表を作る。ほぼすべての解析が使う。
   2. `knn_ood/g_band_correction_augmentation/08_sigma_vs_g_coeffs.py`
      - XP 係数の誤差が G でどう変わるかを調べる。
   3. `knn_ood/g_band_correction_augmentation/09_sigma_fit.py`
      - その関係を 3 次式で当てはめる。G' のコピーのノイズの大きさに使う。
   4. `age_estimation/augmentation_using_age_and_G_band/scripts/age_augmentation.py`
      - Kepler の RGB 2,670 星を読み込み、年齢のコピーを作る（seed 20261106）。
   5. `age_estimation/data_split/scripts/make_split.py`
      - 星を 5 つの fold に分ける（seed 20261110）。
   6. `age_estimation/data_split/scripts/make_fold_data.py`
      - fold ごとの学習・検証・テストのデータを作る。
   - 確かめ方：各ログ（`logs/`）を、git に入っている前のサーバーのログと比べる（行数・星の数が同じか）。
6. **今は要らないもの**
   - APOGEE DR17 allStar と `*_abund.h5`：[C/N] などを使う解析をするときに取得・作り直す。
   - RGB の分類器と k-NN gate の出力（`rgb_classifier/`、`knn_ood/results/knn/`）：年齢のモデルを Gaia の星に当てはめる段階で作り直す。
   - 再現の出力（`repro_runs/` など）：作り直さない。

## 6. ファイルと Notion の場所

**リポジトリ**

| 場所 | 中身 |
|---|---|
| `age_inference_from_xp_spectra/` | この研究のコード（全体の構成はトップの README） |
| 　`train_set_match_parameters/` | fits ↔ h5 の対応表、h5 に APOGEE の値を足したコピー |
| 　`knn_ood/` | k-NN gate と、G バンドのノイズ（暗くしたコピー）の作り方 |
| 　`rgb_classifier/` | XGBoost による RGB の分類 |
| 　`age_estimation/` | 年齢推定（augmentation、データの分け方。モデルはこれから） |
| 　`preliminary/` | 初期の予備実験 |
| 　`report/` | 発表用の図とスライド |
| `astroNN_stars_foundation/` | foundation model の再現（`repro_notes/` に結果・変更点・アイデア） |
| `stellar-age-inference-claude-knowledge/` | この文書と、Claude 用の知識 |

**Notion**（海外インターン → Research at UCL MSSL, UK → 研究進捗）

- 年齢推定：テーマのページ「Age Inference of RGB Stars」（41337d5c362181978d66d6c1155c991c）の配下
  - 年齢推定のための augmentation（41337d5c362181c393a8fc09a95792b4）
  - 年齢推定の学習・検証・テストの分け方（41337d5c36218166bbb4f5a70cf51f2b）
  - foundation model の encoder がしていること（41337d5c362181dcbca6e862e7d0637b）
  - 年齢推定の decoder の作り方の候補（41337d5c362181a3af49fdec6f49ebbc）
- RGB の Age 推定の方針（40f37d5c362181e8a647e4f5628af3eb）
- RGB の分類：XP から RGB を分類：結果（40537d5c3621813386fbd0febb44d2e6）、testing_set での確認（40d37d5c362181609e73d266fd199e73）、XGBoost で良いのか（40d37d5c36218145aba1fddadc3d5e92）
- k-NN gate・G バンド補正・予備実験：研究進捗の各テーマのページ
- 毎日の記録：研究ログ → デイリーログ
- 書き方のルールはメモリ（`notion-writing-rules`）

## 7. 移行が終わったら

1. 次の 2 点を確かめる：Claude がメモリと作業ルールを読めている、5 節が終わっている。
2. 新しいサーバーの `<WORK>/stellar-age-inference-claude-knowledge/` を削除する（ユーザーに確認してから）。
3. GitHub の `stellar-age-inference-claude-knowledge` を Archive するか削除するかを、ユーザーが決めて GitHub の Settings から行う（どちらにするかは移るときに決める）。
4. メモリの中でこのリポジトリに触れている記述（`server-migration-plan` など）を、「移行は完了した」という内容に書き直す。
