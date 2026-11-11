# stellar-age-inference-claude-knowledge

Gaia XP スペクトルからの恒星（RGB）の年齢推定の研究で、Claude Code に引き継ぐ知識（作業ルール・メモリ・再現の規約）を置くリポジトリ。サーバーを移っても、同じルールと経緯を読んだ状態で作業を続けるためのもの。

- `handoff.md`：age inference に入るときの出発点（どこまで終わっていて、次に何をするか）
- `claude/`：Claude が読むファイル
  - `CLAUDE.md`：astroNN_stars_foundation（再現リポジトリ）の規約
  - `CLAUDE.local.md`：個人の作業ルール（2 つのリポジトリで共通）
  - `settings.local.json`：Claude Code の許可の設定
  - `memory/`：メモリ（`MEMORY.md` が索引）。今いるサーバーの条件（`server-current.md`）は git に乗せない
- `setup/`：新しいサーバーで最初に行う手順（`setup.md`）と astroNN の設定ファイル（`astroNN_config.ini`）

新しいサーバーでは `setup/setup.md` のとおりに進める。
