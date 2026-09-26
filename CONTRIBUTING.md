# Contributing Guide

このリポジトリへのコントリビュートの仕方をまとめたガイドです。
実際の OSS でもほぼ同じ流れ(fork → branch → commit → PR → CI → review → merge)で開発が進みます。

## 課題

`participants/<あなたのGitHubユーザー名>.toml` を 1 つ追加する PR を出してください。

```toml
name = "Taro Yamada"            # 表示名(必須)
github = "taro-yamada"          # GitHub ユーザー名。ファイル名と一致させる(必須)
message = "Hello, OSS!"         # 一言メッセージ。140 文字以内(必須)
favorite_oss = "PyTorch"        # 好きな OSS(任意)
```

ルール(CI の `tests/test_participants.py` で自動チェックされます):

- ファイル名は `<GitHubユーザー名>.toml` にする
- `github` の値はファイル名と一致させる
- `name`, `github`, `message` は必須。それ以外に使えるキーは `favorite_oss` のみ
- `message` は 140 文字以内
- 自分のファイル以外は変更しない

## 事前準備

- [git](https://git-scm.com/)
- [uv](https://docs.astral.sh/uv/getting-started/installation/)
- GitHub アカウント(可能なら [GitHub CLI `gh`](https://cli.github.com/) も)

## 手順

### 1. Fork する

GitHub 上でこのリポジトリの右上の **Fork** ボタンを押し、自分のアカウントにコピーを作ります。

```sh
# gh を使う場合はこれだけで fork + clone できます
gh repo fork matsuolab/pai-2026-advance-oss-lecture --clone
```

### 2. Clone して upstream を設定する

```sh
git clone https://github.com/<あなたのユーザー名>/pai-2026-advance-oss-lecture.git
cd pai-2026-advance-oss-lecture
git remote add upstream https://github.com/matsuolab/pai-2026-advance-oss-lecture.git
git remote -v   # origin = 自分の fork, upstream = 本家
```

### 3. 開発環境を用意する

```sh
uv sync                        # 依存関係をインストール
uv run pre-commit install      # git commit 時に pre-commit が自動で走るようにする
```

### 4. ブランチを切る

`master` に直接コミットせず、作業用のブランチを作ります。

```sh
git switch master
git pull upstream master       # 本家の最新を取り込む
git switch -c add-<あなたのユーザー名>
```

### 5. 変更してローカルでチェックする

`participants/<あなたのユーザー名>.toml` を作成したら、CI と同じチェックを手元で実行します。

```sh
uv run pre-commit run --all-files   # フォーマット・lint
uv run pytest                        # テスト
uv run pai-oss-lecture               # 参加者一覧に自分が出るか確認
```

pre-commit がファイルを自動修正した場合(末尾の空白や改行など)は、修正後のファイルを再度 `git add` してください。

### 6. コミットして push する

```sh
git add participants/<あなたのユーザー名>.toml
git commit -m "Add <あなたのユーザー名> to participants"
git push -u origin add-<あなたのユーザー名>
```

### 7. Pull Request を作る

GitHub 上で自分の fork を開くと **Compare & pull request** ボタンが出るので、本家の `master` に向けて PR を作ります。
PR テンプレートのチェックリストを埋めてください。

```sh
# gh を使う場合
gh pr create --repo matsuolab/pai-2026-advance-oss-lecture --fill
```

### 8. CI を通す

PR を作ると GitHub Actions で以下が自動実行されます。

| Job | 内容 |
| --- | --- |
| `pre-commit` | 末尾空白・改行・TOML/YAML の構文・ruff による lint/format |
| `test` | `pytest` で participants のファイルを検証(Python 3.11 / 3.12 / 3.13) |

❌ になったら **Details** からログを見て原因を直し、同じブランチに追加で commit & push してください。PR は自動で更新されます。
(初めてのコントリビュートの場合、メンテナが **Approve and run** を押すまで CI が始まらないことがあります)

### 9. レビューに対応する

レビューでコメントが付いたら、

- 修正して同じブランチに push する
- 対応したらコメントに返信する(「修正しました: <commit hash>」など)
- 納得できない指摘は理由を添えて議論して OK

すべての CI が ✅ になり、レビュアーが Approve したらメンテナがマージします 🎉

### 10. 後片付け(任意)

```sh
git switch master
git pull upstream master
git branch -d add-<あなたのユーザー名>
git push origin --delete add-<あなたのユーザー名>
```

## よくあるトラブル

- **`github` must match the file name**: ファイル名と `github` の値がずれています(大文字・小文字も区別されます)。
- **pre-commit が `Failed` で commit できない**: 自動修正されたファイルを `git add` し直して、もう一度 commit してください。
- **PR に他人の変更や余計なファイルが含まれている**: `master` から新しくブランチを切り直し、自分のファイルだけを commit してください。
- **本家が更新されてコンフリクトした**: `git pull upstream master` で最新を取り込み、解消して push してください。
