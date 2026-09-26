# pai-2026-advance-oss-lecture

OSS 開発の流れ(**fork → branch → commit → Pull Request → CI → review → merge**)を体験するためのチュートリアル用リポジトリです。

受講生は `participants/` に自分のプロフィールファイルを 1 つ追加する PR を出し、CI を通してレビューを受け、マージされるまでを体験します。

👉 **手順は [CONTRIBUTING.md](CONTRIBUTING.md) を読んでください。**

## 構成

```
.
├── participants/              # 受講生が <GitHubユーザー名>.toml を追加する場所
├── src/pai_oss_lecture/       # participants を読み込み・検証する Python パッケージ
├── tests/                     # pytest(CI で participants の書式を検証)
├── .pre-commit-config.yaml    # pre-commit(ruff, 空白・改行, TOML/YAML チェック)
└── .github/
    ├── workflows/ci.yml       # GitHub Actions: pre-commit + pytest
    └── pull_request_template.md
```

## 開発

```sh
uv sync
uv run pre-commit install
uv run pre-commit run --all-files
uv run pytest
uv run pai-oss-lecture   # 参加者一覧を表示
```
