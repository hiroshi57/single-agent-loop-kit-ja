# 自分のプロジェクト群への適用手順

このリポジトリの `skill/` は Apache-2.0 のまま、各プロジェクトの
`.claude/skills/single-agent-loop/` にコピーして使う設計になっています
(詳細は [`README.upstream.en.md`](../README.upstream.en.md) の
"Use it on your own system")。

## 手順

```bash
# 対象プロジェクトのルートで実行
git clone https://github.com/<あなたのGitHubアカウント>/single-agent-loop-kit-ja /tmp/salk
cp -r /tmp/salk/skill "<対象プロジェクト>/.claude/skills/single-agent-loop"
```

その後、対象プロジェクトで Claude Code を開き「Run the single-agent-loop skill.」
と伝えると、`skill/steps/00-audit.md` から監査が始まります。最初の stop
(check-in)で「このサブエージェントは fold / merge / ツール化 / 維持」の
判定一覧が出るので、そこで止めて確認してください(課金が発生する評価実行は
その後の stop で明示的に yes を取ってから)。

## 最有力候補: 自分のClaude Codeハーネス自体(`/harness-work`)

`CLAUDE.md` の「6. 利用可能なコマンド」にある `/harness-plan` / `/harness-work` /
`/harness-review` / `/harness-release` の仕組み、特に `/harness-work` が内部で
使っている **Lead(オーケストレーター) → Worker(実装) → Reviewer(検証)** という
構成は、このキットが対象とする「ルーター/オーケストレーター + 専門サブエージェント」
の典型パターンです。複数プロジェクト(dev-agent-platform・yosikei-agents 等)で
横断的に使われているため、ここを監査する効果が一番大きい可能性があります。

**ただし早合点で畳まないこと。** 記事が挙げる「それでもサブエージェントが要る
3つの場面」のうち、少なくとも2つに該当しそうです。

- Reviewer は実装者(Worker)とは**独立した検証**という位置付け
  (`.claude/rules/judge-rubric.md` 等を参照。Worker の自己申告を判定根拠にしない、
  という既存ルールとも整合的)
- 複数の Worker を**並行**に走らせるモード(`parallel_mode: team` 等)がある

これらは記事が明示する「サブエージェントを残す正当な理由」に当てはまる可能性が
高いため、**監査(step 0)で各サブエージェントに証跡付きの判定を出してから**
判断してください。「なんとなく分けている」のか「情報の独立性ゆえに分けている」
のかを、コードとログから切り分けるのがこのキットの役目です。

監査の材料は既にこのハーネスに揃っています(読み替え表):

| このキットが使うもの | ハーネス内の対応物 |
|---|---|
| `skill/scripts/coupling_map.py` の入力(トレース) | `.claude/harness-logs/<project-slug>/<YYYYMM>/<task_id>/` の `worker-report.json` / `review.json` |
| eval(正解付きタスク集) | 過去の `task.json` + その `review.json` の判定(APPROVE/REQUEST_CHANGES)を正解ラベルとして束ねれば、たたき台になる可能性があります |
| `scripts/chain_coverage.py` | Worker→Reviewer→Lead の一連の流れを漏れなくカバーしているかの確認に使える |

## そのほかの候補(未監査・要確認)

- **yosikei-agents の `agents/orchestrator.py`**: 媒体別(Google/Yahoo/Microsoft/
  Meta/TikTok/YDA/SmartNews/GA4)に処理を分けている構成。これは「問題の種類」で
  分けているように見えますが、各媒体の生データ形式がそれぞれ全く異なり、
  相互に情報をほぼ共有しないなら「必要な情報のまとまりで分けている」正当な
  並行処理である可能性もあります。本リポジトリでは未確認のため、実装を
  開いて監査してから判断してください。
- 上記以外にサブエージェント/ルーターを持つプロジェクトがあれば、同様に
  `skill/steps/00-audit.md` から始めてください。

## 注意

- 評価実行(eval)はモデル課金が発生します。`skill/SKILL.md` の check-in
  ルールに従い、**課金を伴う実行の前に必ずサイズとトークン見積りを確認**
  してから yes を出してください。
- 本ハーネス運用では「本ファイル(CLAUDE.md)や各案件のskillsへの直接書き込みは
  人間の承認を経てから」という既存ルールがあります。`/harness-work` 自体を
  改修する場合もこの原則を踏襲し、監査結果の提示 → 人間の承認 → 実装、の順で
  進めてください。
