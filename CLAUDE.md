# CLAUDE.md

このファイルは Claude Code が pov-harness リポジトリで作業する際のガイダンスを提供する。

---

## プロジェクト概要

**リポジトリ**: https://github.com/inadati/pov-harness
**種別**: Claude Code プラグイン
**目的**: PoV（Point of View）駆動の開発ハーネス。要件定義 → 実装プラン → 審査団編成 → 実装 → 多観点評価 → 修正ループで品質を担保する。

---

## ディレクトリ構造

```
pov-harness/
├── plugins/
│   └── pov-harness/
│       └── skills/
│           ├── wantree/
│           │   └── SKILL.md       ← ヒアリング形式で wantree.yml を作成・管理
│           ├── plan-review/
│           │   └── SKILL.md       ← ExitPlanMode 後に AUTO-TRIGGER されるレビュースキル
│           └── pov-harness/
│               └── SKILL.md       ← メインスキル（7フェーズの実行手順書）
├── README.md
└── CLAUDE.md（このファイル）
```

---

## スキル一覧

| スキル | 呼び出し | 役割 |
|--------|---------|------|
| `wantree` | `/pov-harness:wantree` | ヒアリング形式で要件定義YAML（wantree.yml）を作成・管理 |
| `plan-review` | AUTO-TRIGGER のみ | `ExitPlanMode` 後に自動起動。プランを最小決定事項に分解して1件ずつ確認 |
| `pov-harness` | `/pov-harness:pov-harness` | メインスキル。7フェーズで要件定義〜審査団ループを実行 |

---

## pov-harness スキルの7フェーズ

```
フェーズ0: 起動確認（A=要件定義から / B=プランあり / C=コードあり）
フェーズ1: wantree インライン実行（フェーズ0でAを選択した場合のみ）
フェーズ2: 実装プランニング（EnterPlanMode）
フェーズ3: plan-review AUTO-TRIGGER（1回目）
フェーズ4: 審査団プランニング（EnterPlanMode）
フェーズ5: plan-review AUTO-TRIGGER（2回目）
フェーズ6: .pov-harness/committee.yml に書き出し
フェーズ7: 実装ループ（実装→並列PoV評価→審査団長集約→合否判定、上限5回）
```

---

## 重要な設計決定（なぜこうなっているか）

### plan-review が2回走る理由

| 回 | 対象 | 目的 |
|----|------|------|
| 1回目 | 実装プラン | 技術的方針・設計の確認 |
| 2回目 | 審査団プラン | PoV構成・合格ラインの確認 |

実装プランが確定した後でないと審査団の構成が決められないため、必ず直列で実行する。

### passing_score を委員ごとに個別設定する理由

「セキュリティ vs UX」のように競合するPoVが存在する場合、総合点の閾値を下げても意味がない。セキュリティが70点という事実は変わらないため。**正しい解決策は競合するPoV同士のそれぞれの合格点を個別に設定すること**。競合がない場合は全員100点のまま。

### wantree をインライン実行する理由

スキルから別スキルを呼ぶ公式手段がない。フロー継続性を保つため、wantree スキルの手順を pov-harness SKILL.md 内に直接埋め込んでいる。

### PoV エージェントを並列起動する理由

委員ごとに独立した評価を並列 `Agent` ツールで実行することで、相互影響なく公平な採点を行いつつ、処理時間を短縮する。

---

## ターゲットプロジェクト側の生成物

`/pov-harness` を実行したプロジェクトルートに以下が生成される:

```
プロジェクトルート/
├── .wantree/
│   └── 0/
│       └── wantree.yml    ← 要件定義（フェーズ1）
└── .pov-harness/
    └── committee.yml      ← 審査団設定（フェーズ6）
```

---

## 開発履歴

### 2026-04-23（PR #2 マージ）

- `pov-harness/SKILL.md` を設計ドキュメントから **7フェーズの実行手順書** に全面書き直し
- frontmatter の `tools` に `EnterPlanMode`, `ExitPlanMode`, `Glob` を追加
- フェーズ7aに「Agent ツールを積極活用して効率的に実装する」指針を追加

### 2026-04-07以前（PR #1）

- pov-harness プラグインの初期設計
- wantree・plan-review・pov-harness の3スキル構成を確立
- plan-review の AUTO-TRIGGER 設計を確立

---

## 次のステップ

- `sandbox/pov-harness/` でデモプロジェクトを用意して実際に動作検証する
- 各フェーズが正しく動作することを確認（検証チェックリストは以下）:
  1. フェーズ0の選択肢（A/B/C）が表示される
  2. Aを選んでwantreeがインライン起動する
  3. 実装プランが作成されplan-reviewがAUTO-TRIGGERされる
  4. 審査団プランが作成されplan-reviewが2回目に起動する
  5. `.pov-harness/committee.yml` が生成される
  6. 並列Agentが起動し採点・審査団長集約が行われる

---

## メモリに保存すべき重要情報

このプロジェクトを開いたときは以下をメモリに記録しておくこと:

- **pov-harness は Claude Code プラグイン**。ユーザーが他プロジェクトで `/pov-harness:pov-harness` 等を呼び出して使う
- **SKILL.md は実行手順書**。設計資料ではなく Claude がそのまま読んで実行する指示書として書かれている
- **plan-review は AUTO-TRIGGER 専用**。ユーザーが手動で呼び出すスキルではない
- **wantree.yml の置き場所**: ターゲットプロジェクトルートの `.wantree/<番号>/wantree.yml`
- **committee.yml の置き場所**: ターゲットプロジェクトルートの `.pov-harness/committee.yml`
- **ループ上限は5回**（SKILL.md 本文が優先。プランの「確認事項」に3回と書かれていたが5回に変更済み）
