# single-agent-loop-kit-ja

[anthropics/single-agent-loop-kit](https://github.com/anthropics/single-agent-loop-kit)
(Apache-2.0) の日本語フォークです。コード・英語の手順書は取得元コミットのまま、
日本語ドキュメント層(本README・`docs/`)を追加しています。詳細は
[`NOTICE.md`](NOTICE.md) を参照してください。

元ネタの記事: Anthropic公式 "Building agents: When to fold subagents into your main
agent loop"(2026-10-22)。要点は [`docs/article-summary.ja.md`](docs/article-summary.ja.md)
にまとめています。

## これは何か

ルーター/オーケストレーターが専門サブエージェントに処理を振る「マルチエージェント構成」
を、**測定しながら**少しずつ「1つのエージェントループがスキル/ツールをオンデマンドで
読み込む構成」へ畳み込んでいくための Claude Code スキルと付属スクリプト一式です。

**「全部畳め」ではありません。** 変更の前後で必ず同じ評価(eval)を回してスコアを比べ、
数字が裏付けたときだけサブエージェントを置き換えます。サブエージェントを維持する、
という結論も正当な結果の一つです。

## 考え方(3段階)

1. **測る** — 今の構成を今あるevalで採点する。evalが無ければこのキットが一緒に作る。
2. **まとめる** — いつもセットで動くサブエージェントのペアを統合する。
3. **畳む** — 残ったサブエージェントを1つずつ「スキル + ツール」に変換し、都度比較する。
   自前のコンテキストに値するものだけ残し、それはツールとして呼び出す。

その後は健全性を保つ運用に入ります: システムプロンプトと共有evalは小さな中央チームが
持ち、各スキルはそれを作ったドメインチームが持つ。

## 使い方

### 1. まず自分の構成を監査してみる(10分、無料)

同梱の実行可能な例 `examples/support-desk`(ルーター+6サブエージェント構成)を
手元で動かして、Claude Codeがそれを監査し、1つ畳んで前後の数値を出すところまで
体験できます。英語ですが手順は [`examples/support-desk/RUNBOOK.md`](examples/support-desk/RUNBOOK.md)
にあります。APIキー不要・無料で動く疎通確認:

```bash
cd examples/support-desk
KIT_MODEL=fake python3 evals/run_eval.py --variant smoke
```

### 2. 自分のプロジェクトに適用する

[`docs/usage-for-my-projects.ja.md`](docs/usage-for-my-projects.ja.md) に、
自分が運用している他のリポジトリ(Claude Codeハーネスを使っているプロジェクト群)
へこのskillをどう適用するかの手順と、最有力候補(`/harness-work` の
Lead/Worker/Reviewer構成)についてのメモをまとめています。

基本の流れ:

```bash
git clone <このリポジトリのURL> /tmp/salk
cp -r /tmp/salk/skill "<対象プロジェクト>/.claude/skills/single-agent-loop"
```

その後、対象プロジェクトでClaude Codeを開き「Run the single-agent-loop skill.」と
伝えると、`skill/steps/00-audit.md` から監査が始まります。

## 中身

| パス | 内容 |
|---|---|
| [`skill/SKILL.md`](skill/SKILL.md) | 入口。各ステップファイルへ誘導する |
| [`skill/steps/`](skill/steps) | 監査→ベースライン→統合→畳み込み→ツール化→保守、の6ステップ |
| [`skill/scripts/`](skill/scripts) | トレース分析・評価比較・チェーン網羅チェック・トークン見積りの4スクリプト(無依存Python) |
| [`skill/templates/`](skill/templates) | 棚卸し表・計画書・スキル雛形・ツール仕様書等のテンプレート |
| [`reference_loop/loop.py`](reference_loop/loop.py) | 単一ループの最小リファレンス実装(並列ツールバッチ・スキル/ツールのオンデマンド読込・予算管理・書き込みゲート・検証器) |
| [`examples/support-desk/`](examples/support-desk) | 動かして試せるサンプル(畳み込み前`app/`・畳み込み後`single_loop/`) |
| [`docs/article-summary.ja.md`](docs/article-summary.ja.md) | 元記事の日本語ダイジェスト+チェックリスト(独自追加) |
| [`docs/usage-for-my-projects.ja.md`](docs/usage-for-my-projects.ja.md) | 自分のプロジェクト群への適用ガイド(独自追加) |
| [`README.upstream.en.md`](README.upstream.en.md) | 取得元の英語README(そのまま保持) |

## テスト

```bash
python3 -m unittest discover tests
```

APIキー不要。スクリプト・リファレンスループ・サンプルの両バージョンのオフライン実行を
カバーします(フォーク時点で48件すべてPASSを確認済み)。

## このキットが要らない場合

- サブエージェントがまだ一つも無い: いきなりこのキットではなく
  `reference_loop/loop.py` を読んで、テストで必要性が出てから道具とスキルを足す。
- サブエージェントがそれぞれ自前のコンテキストに値している
  (独立した並行調査・独立検証・メインが読む必要のない大量の情報を扱っている等):
  監査がそう教えてくれます。
- まだ採点できない(evalが無い): それ自体は問題ありません。ベースラインのステップが
  一緒に作ります。ただし本番相当のリクエストで動かす手段と、何が正解かを言える人、
  この2つだけはこちらで用意してください。

## ライセンス

Apache-2.0。[`LICENSE`](LICENSE) と [`NOTICE.md`](NOTICE.md) を参照。
