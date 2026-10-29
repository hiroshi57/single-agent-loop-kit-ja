# NOTICE

このリポジトリは [anthropics/single-agent-loop-kit](https://github.com/anthropics/single-agent-loop-kit)
(Copyright 2026 Anthropic PBC, Apache License 2.0) を取得元コミットの状態のまま
フォークし、日本語ドキュメント層を追加したものです。

> 参照元リポジトリ自身が "Fork it and adapt it to your own system."(フォークして自分の
> システムに合わせて改造してよい)と明記しているため、本フォークはその想定どおりの利用です。
> 参照元は "Not maintained and not accepting contributions"(メンテナンスなし・PR非受付)
> のリファレンス実装であり、本フォークも同様に一次ソースの更新までは追従しません。

## このフォークで変更した点

- **変更していないもの**: `skill/`・`reference_loop/`・`examples/`・`tests/`・`LICENSE`・
  `CLAUDE.md` は取得元コミットのまま(コード・英語の手順書はそのまま忠実に保持)。
  これは意図的な判断です。`skill/steps/*.md` はClaude Codeの挙動を一語単位で細かく
  制御するプロセス文書であり、翻訳による意味のずれ(特にcheck-inの書式や"stop"の定義)が
  運用事故に直結するため、実行対象のテキストは原文を正とします。
- **追加したもの**(本フォークの独自部分、日本語):
  - [`README.md`](README.md) — 新規の日本語フロントページ。元の英語READMEは
    [`README.upstream.en.md`](README.upstream.en.md) として保持
  - [`docs/article-summary.ja.md`](docs/article-summary.ja.md) — 発端となった記事
    「Building agents: When to fold subagents into your main agent loop」の要点を
    日本語でまとめた独自ダイジェスト
  - [`docs/usage-for-my-projects.ja.md`](docs/usage-for-my-projects.ja.md) — 自分の
    既存プロジェクト群(Claude Codeハーネス運用中のリポジトリ)へこのskillをどう
    適用するかの手順書
  - 本 `NOTICE.md`

## 取得元コミット

フォーク作成日時点の `anthropics/single-agent-loop-kit` の `main` ブランチ最新コミット
(`git clone --depth 1` で取得)。最新の差分・修正は取得元リポジトリを直接参照してください。

## ライセンス

Apache License 2.0。詳細は [`LICENSE`](LICENSE) を参照。著作権表示は元のまま保持しています。
