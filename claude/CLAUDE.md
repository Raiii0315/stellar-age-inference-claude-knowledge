# astroNN_stars_foundation 再現リポジトリ

Leung & Bovy 2023（MNRAS 527, 1494）の結果を再現するためのリポジトリ。
元リポジトリ: https://github.com/henrysky/astroNN_stars_foundation

**このリポジトリの目的は再現のみ。** 新しい手法の追加や派生研究は別リポジトリで行うので、ここでは行わない。改良案や派生のアイデアを思いついた場合は、実装せずに `repro_notes/ideas.md` にメモするだけにとどめる。

## サーバーごとの条件

- ストレージの置き場所と制約、GPU の使い方、環境変数（uv・キャッシュ・データ置き場）はサーバーごとに違うので、メモリの `server-current.md` を見る。
- 作業ディレクトリの親（2つのリポジトリが並ぶ場所）を `<WORK>` と書く。

## Python 環境

- 環境は **uv** で管理する。`pip install` は使わず、パッケージ追加は `uv add <pkg>`、実行は `uv run python ...` を使う。
- Python 3.10（`.python-version` で固定）。元論文の Docker 環境 `nvcr.io/nvidia/pytorch:24.08-py3` に合わせている。
- torch 2.5.1（PyTorch 公式インデックスから取得。設定は `pyproject.toml` の `[tool.uv.sources]` と `[[tool.uv.index]]`）。
- `numpy<2` に固定（2024年当時の astro 系パッケージとの互換性のため）。
- 再現性の記録は `pyproject.toml`・`.python-version`・`uv.lock` の3つ。依存を変えたら `uv.lock` もコミットする。
- **パッケージのバージョンを変える・追加する場合は、理由を `repro_notes/deviations.md` に記録する**（元環境との差分になるため）。
- uv 関連の環境変数（`UV_CACHE_DIR`、`UV_PYTHON_INSTALL_DIR` など）は `~/.bashrc` で設定する（値は `server-current.md`）。`uv: command not found` が出たら `~/.bashrc` が読まれていない可能性が高い。

## 元リポジトリ由来の注意点

- astroNN の設定ファイルで `magicnumber = nan` にする必要がある（データ整形コードが正しく動くため）。
- データセット（`training_set.h5`、`testing_set.h5`）は Zenodo（https://zenodo.org/records/12738256）から取得し、`data_files/` に置く。`Dataset_Reduction.ipynb` で自前生成すると TB 級のダウンロードが必要なので、Zenodo 版を使う。
- 一部のノートブックは比較用に以下を必要とする:
  - Zhang et al. 2023 のモデル（TensorFlow が必要）→ `zhanggreenrix2023_stellar_flux_model/`。TensorFlow は torch と依存が衝突しやすいので、別の venv に分ける。
  - Andrae et al. 2023 のカタログ `table_2_catwise.fits.gz` → `andae2023_catalog/`。
- `mwdust` は README に記載があるが requirements.txt には無い。データ再構築時のみ必要で、ビルドに GSL が要る。
- 図の描画は LaTeX（type1cm、cm-super、dvipng）を使う設定になっている。

## データ置き場の環境変数（Dockerfile と同等）

`MY_ASTRO_DATA`、`SDSS_LOCAL_SAS_MIRROR`、`GAIA_TOOLS_DATA`、`DUST_DIR` を設定する。値はサーバーごとに `server-current.md` に書く。

## 再現作業のルール

- **元コード（`stellarperceptron/`、`utils/`、`training.py`、各ノートブック）は原則として改変しない。** 動かすためにどうしても修正が必要な場合は、最小限の変更にとどめ、何をなぜ変えたかを `repro_notes/deviations.md` に記録する。
- ノートブックは元ファイルを直接書き換えず、実行結果を残したい場合はコピー（例: `repro_runs/Inference_Spec2Labels_run1.ipynb`）を作って実行する。
- 元の学習済みモデル（`model_torch/`、`model_torch_search/`）は上書きしない。再学習した場合は別のフォルダ（例: `repro_models/`）に保存する。
- 乱数シード、使用した GPU、実行時間など、結果に影響しうる条件を記録する。

## 結果の記録

- 再現した数値は、論文の値と並べて `repro_notes/results.md` に記録する。表の列は「項目 / 論文の値 / 再現値 / 差 / 備考」とする。
  - 例: Gaia XP → 恒星パラメータの精度（論文: Teff 47 K、logg 0.11 dex、[M/H] 0.07 dex）
- 論文と一致しない場合は、原因の推測（環境差、乱数、データ版の違いなど）も書く。
- 再現した図は `figs/` に保存し、論文の図番号をファイル名に含める（例: `fig03_spec2labels.pdf`）。

## 記録用ファイルの構成

```
repro_notes/
├── results.md      # 論文の値と再現値の比較
├── deviations.md   # 元コード・元環境からの変更点とその理由
└── ideas.md        # 派生研究のアイデア（実装はしない）
repro_runs/         # 実行したノートブックのコピー
repro_models/       # 再学習したモデル（行う場合）
```
