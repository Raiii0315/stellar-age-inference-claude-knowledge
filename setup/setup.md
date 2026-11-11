# 新しいサーバーで最初に行う手順

新しいサーバーで `claude` を起動した直後の Claude は、これまでの議論も作業ルールも知らない。
メモリと `CLAUDE.local.md` などを所定の場所に置くまでは、経緯を踏まえた提案ができない。
そこで、**Claude が知識を読める状態になるまでの数手だけ**を、ここに書いておき、手で行う。
そのあと（Python の環境、データ、解析の出力の再実行）は、Claude が `handoff.md` を読んで 1 つずつ提案する。

前提（済んでいるもの）：新しいサーバーへの SSH、新しいサーバーから GitHub への SSH、uv、Claude Code。

以下、親ディレクトリ（名前は自由）を `<WORK>` と書く。

## 1. 3 つのリポジトリを clone する

理由：研究のコードと、Claude 用の知識（このリポジトリ）を新しいサーバーに持ってくるため。

```bash
mkdir <WORK> && cd <WORK>
git clone git@github.com:Raiii0315/age_inference_from_xp_spectra.git
git clone git@github.com:Raiii0315/astroNN_stars_foundation_repro.git astroNN_stars_foundation
git clone git@github.com:Raiii0315/stellar-age-inference-claude-knowledge.git
```

- 再現リポジトリは、GitHub の名前（`astroNN_stars_foundation_repro`）ではなく `astroNN_stars_foundation` という名前で clone する。理由：`age_inference_from_xp_spectra/paths.py` が「同じ階層の `astroNN_stars_foundation/`」を探すため。
- 3 つを同じ親ディレクトリの下に並べる。理由：同じく `paths.py` がこの並びを前提にしているため。

## 2. メモリを所定のフォルダにコピーする

理由：Claude は、**起動したディレクトリの絶対パス**から決まるフォルダのメモリを読むため。新しいサーバーでは `age_inference_from_xp_spectra` で起動するので、そのパスに対応するフォルダに置く。

フォルダの場所：

- Claude の設定フォルダ：環境変数 `CLAUDE_CONFIG_DIR` があればそこ、なければ `~/.claude`
- その下の `projects/<名前>/memory/`。`<名前>` は、起動するディレクトリの絶対パスの英数字以外を `-` に変えたもの

```bash
cd <WORK>/age_inference_from_xp_spectra
NAME=$(pwd | sed 's/[^A-Za-z0-9]/-/g')
CFG=${CLAUDE_CONFIG_DIR:-$HOME/.claude}
ls $CFG/projects/$NAME 2>/dev/null     # 何も出なければ、まだない（上書きの心配なし）
mkdir -p $CFG/projects/$NAME
cp -r <WORK>/stellar-age-inference-claude-knowledge/claude/memory $CFG/projects/$NAME/memory
```

- clone した場所を後から動かすと、パスが変わってメモリが読まれなくなる。動かしたら、このフォルダも合わせて名前を変える。

## 3. 作業ルールと規約のファイルを置き、git に乗らないようにする

理由：`CLAUDE.local.md`（個人の作業ルール）と `CLAUDE.md`（再現リポジトリの規約）は、Claude が起動時やそのディレクトリを触ったときに読む。どちらも GitHub には上げないファイルなので、git の除外に足しておく（足さないと、誤って push してしまうおそれがある）。

```bash
K=<WORK>/stellar-age-inference-claude-knowledge/claude
cd <WORK>

# 個人の作業ルール（2 つのリポジトリに同じものを置く）
cp $K/CLAUDE.local.md age_inference_from_xp_spectra/
cp $K/CLAUDE.local.md astroNN_stars_foundation/

# 再現リポジトリの規約
cp $K/CLAUDE.md astroNN_stars_foundation/

# Claude Code の許可の設定（Notion のページの更新を毎回聞かずに行う）
mkdir -p age_inference_from_xp_spectra/.claude astroNN_stars_foundation/.claude
cp $K/settings.local.json age_inference_from_xp_spectra/.claude/
cp $K/settings.local.json astroNN_stars_foundation/.claude/

# git の除外（.gitignore ではなく、自分の手元だけの .git/info/exclude に足す）
printf 'CLAUDE.local.md\n.claude/settings.local.json\n' >> age_inference_from_xp_spectra/.git/info/exclude
printf 'CLAUDE.md\nCLAUDE.local.md\n.claude/settings.local.json\nandae2023_catalog/\nzhanggreenrix2023_stellar_flux_model/\n' >> astroNN_stars_foundation/.git/info/exclude
```

確かめ方：それぞれのリポジトリで `git status` を実行し、上のファイルが出てこなければよい。

## 4. Claude を起動する

```bash
cd <WORK>/age_inference_from_xp_spectra && claude
```

- `/init` は実行しない。理由：`CLAUDE.md` を新しく作るコマンドで、持ち込んだものと食い違うおそれがあるため。
- 初めて起動したときは claude.ai のアカウントでログインする。Notion は claude.ai 側でつないであれば、同じアカウントで使える。

最初に Claude に頼むこと：

1. このサーバーの条件（GPU・CPU・ストレージ・ホームの容量・必要な環境変数）を調べて、メモリに `server-current.md` を作る（git には乗らない）。
2. `<WORK>/stellar-age-inference-claude-knowledge/handoff.md` を読んで、続き（Python の環境、データ、解析の出力の再実行、age inference）を 1 つずつ提案してもらう。
