# CLAUDE.md

このファイルは Claude Code が i-harness リポジトリで作業する際のガイダンスを提供する。

---

## プロジェクト概要

**リポジトリ**: https://github.com/inadati/i-harness
**種別**: Claude Code プラグイン
**目的**: 指標（indicator）駆動の開発ハーネス。要件定義 → 実装プラン → 審査団編成 → 実装 → 多指標評価 → 修正ループで品質を担保する。

---

## ディレクトリ構造

```
i-harness/
├── plugins/
│   └── i-harness/
│       └── skills/
│           ├── wantree/
│           │   └── SKILL.md       ← ヒアリング形式で wantree.yml を作成・管理
│           ├── plan-review/
│           │   └── SKILL.md       ← ExitPlanMode 後に AUTO-TRIGGER されるレビュースキル
│           └── i-harness/
│               └── SKILL.md       ← メインスキル（7フェーズの実行手順書）
├── README.md
└── CLAUDE.md（このファイル）
```

---

## スキル一覧

| スキル | 呼び出し | 役割 |
|--------|---------|------|
| `wantree` | `/i-harness:wantree` | ヒアリング形式で要件定義YAML（wantree.yml）を作成・管理 |
| `plan-review` | AUTO-TRIGGER のみ | `ExitPlanMode` 後に自動起動。プランを最小決定事項に分解して1件ずつ確認 |
| `i-harness` | `/i-harness:i-harness` | メインスキル。7フェーズで要件定義〜審査団ループを実行 |

---

## i-harness スキルの7フェーズ

```
フェーズ0: 起動確認（A=要件定義から / B=プランあり / C=コードあり）
フェーズ1: wantree インライン実行（フェーズ0でAを選択した場合のみ）
フェーズ2: 実装プランニング（EnterPlanMode）
フェーズ3: plan-review AUTO-TRIGGER（1回目）
フェーズ4: 審査団プランニング（EnterPlanMode）
フェーズ5: plan-review AUTO-TRIGGER（2回目）
フェーズ6: .i-harness/committee.yml に書き出し
フェーズ7: 実装ループ（実装→並列指標評価→審査団長集約→合否判定、上限5回）
```

---

## 重要な設計決定（なぜこうなっているか）

### plan-review が2回走る理由

| 回 | 対象 | 目的 |
|----|------|------|
| 1回目 | 実装プラン | 技術的方針・設計の確認 |
| 2回目 | 審査団プラン | 指標構成・合格ラインの確認 |

実装プランが確定した後でないと審査団の構成が決められないため、必ず直列で実行する。

### passing_score を指標ごとに個別設定する理由

「セキュリティ vs UX」のように競合する指標が存在する場合、総合点の閾値を下げても意味がない。**正しい解決策は競合する指標同士のそれぞれの合格点を個別に設定すること**。競合がない場合は全員100点のまま。

### チェックリスト採点方式を採用する理由

評価のたびに採点ポイントがブレる問題を解消するため、審査団プランニング時に判定基準（criteria）を固定化する。`score = 達成基準数 / 全基準数 × 100` の式により採点が客観的になり、100点が現実的な目標となる。

### wantree をインライン実行する理由

スキルから別スキルを呼ぶ公式手段がない。フロー継続性を保つため、wantree スキルの手順を i-harness SKILL.md 内に直接埋め込んでいる。

### 指標エージェントを並列起動する理由

指標ごとに独立した評価を並列 `Agent` ツールで実行することで、相互影響なく公平な採点を行いつつ、処理時間を短縮する。

---

## ターゲットプロジェクト側の生成物

`/i-harness:i-harness` を実行したプロジェクトルートに以下が生成される:

```
プロジェクトルート/
├── .wantree/
│   └── 0/
│       └── wantree.yml    ← 要件定義（フェーズ1）
└── .i-harness/
    └── committee.yml      ← 審査団設定（フェーズ6）
```

---

## 開発履歴

### 2026-04-24（v0.3.0）

- リポジトリ・プラグイン名を `pov-harness` → `i-harness` に改名
- `role` → `indicator`、`items` → `criteria` に用語統一
- 「観点（PoV）駆動」から「指標（indicator）駆動」へコンセプトを明確化

### 2026-04-24（v0.2.0）

- チェックリスト駆動評価を導入（`score = 達成基準数 / 全基準数 × 100`）
- 評価のブレを解消するため、審査団プランニング時に判定基準を固定化

### 2026-04-23（v0.1.3）

- `default-committee.yml` を廃止。wantree.yml を参照して指標を提案する方式に変更

### 2026-04-23（v0.1.2）

- `default-committee.yml` をベースとして全採用する方針に修正（その後廃止）

### 2026-04-23（PR #2）

- SKILL.md を設計ドキュメントから7フェーズの実行手順書に全面書き直し

---

## メモリに保存すべき重要情報

このプロジェクトを開いたときは以下をメモリに記録しておくこと:

- **i-harness は Claude Code プラグイン**。ユーザーが他プロジェクトで `/i-harness:i-harness` 等を呼び出して使う
- **SKILL.md は実行手順書**。設計資料ではなく Claude がそのまま読んで実行する指示書として書かれている
- **plan-review は AUTO-TRIGGER 専用**。ユーザーが手動で呼び出すスキルではない
- **wantree.yml の置き場所**: ターゲットプロジェクトルートの `.wantree/<番号>/wantree.yml`
- **committee.yml の置き場所**: ターゲットプロジェクトルートの `.i-harness/committee.yml`
- **ループ上限は5回**
- **スキルのバージョン変更時**: SKILL.md frontmatter・marketplace.json・plugin.json の3ファイルを必ず同期する
