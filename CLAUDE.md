# CLAUDE.md

このファイルは Claude Code が asw-marketplace リポジトリで作業する際のガイダンスを提供する。

---

## プロジェクト概要

**リポジトリ**: https://github.com/inadati/asw-marketplace
**種別**: Claude Code プラグイン集
**目的**: Claude Code の開発体験を向上させるユーティリティスキル群。

---

## ディレクトリ構造

```
asw-marketplace/
├── plugins/
│   ├── asw-utils/
│   │   └── skills/
│   │       ├── wantree/
│   │       │   ├── SKILL.md       ← ヒアリング形式で wantree.yml を作成・管理
│   │       │   └── references/
│   │       │       └── template.yml
│   │       ├── plan-review/
│   │       │   └── SKILL.md       ← ExitPlanMode 後に AUTO-TRIGGER されるレビュースキル
│   │       ├── easy-evaluation/
│   │       │   └── SKILL.md       ← 提案を複数評価者に批評させるスキル
│   │       └── intent-check/
│   │           └── SKILL.md       ← AI の意図推論とユーザーの思惑の齟齬を確認するスキル
│   └── crucible-harness/
│       └── skills/
│           ├── crucible-harness/
│           │   └── SKILL.md       ← Phase 0: セットアップ（/crucible-harness）
│           └── run/
│               └── SKILL.md       ← Phase 1-5: 実行ループ（/crucible-harness:run）
├── README.md
└── CLAUDE.md（このファイル）
```

---

## スキル一覧

| スキル | 呼び出し | 役割 |
|--------|---------|------|
| `wantree` | `/asw-utils:wantree` | ヒアリング形式で要件定義YAML（wantree.yml）を作成・管理 |
| `plan-review` | AUTO-TRIGGER のみ | `ExitPlanMode` 後に自動起動。プランを最小決定事項に分解して1件ずつ確認 |
| `easy-evaluation` | `/asw-utils:easy-evaluation` | 提案を複数評価者（サブエージェント）に並列で批評させる |
| `intent-check` | `/asw-utils:intent-check` | 素材（画像・テキスト等）からAIが推論した意図とユーザーの思惑を1件ずつ照合 |
| `crucible-harness` | `/crucible-harness` | Phase 0: タスク定義・チェックリスト・パラメータのセットアップ |
| `crucible-harness:run` | `/crucible-harness:run` | Phase 1-5: 固定チェックリスト×悪魔の代理人×Self-Reflection×Memoryの品質改善ループ |

---

## 重要な設計決定

### plan-review は AUTO-TRIGGER 専用

`ExitPlanMode` 後に自動起動するスキル。ユーザーが手動で呼び出すスキルではない。

### wantree.yml の置き場所

ターゲットプロジェクトルートの `.wantree/<番号>/wantree.yml`

---

## スキルのバージョン変更時

SKILL.md frontmatter・marketplace.json・plugin.json の3ファイルを必ず同期する。

---

## 開発履歴

### 2026-04-27（v0.4.0）

- リポジトリ名を `i-harness` → `asw-utils` に変更
- `i-harness` スキルを廃止。`wantree` と `plan-review` のみに整理
- プラグイン名を `i-harness` → `asw-utils` に変更

### 2026-05-04（v0.7.0）

- リポジトリ名を `asw-utils` → `asw-marketplace` に変更（マーケットプレイスの概念を明確化）
- プラグイン名（`asw-utils`）は変更なし

### 2026-05-04（v0.6.0）

- リポジトリ名を `asw-plant` → `asw-utils` に戻す
- プラグイン名を `asw-plant` → `asw-utils` に戻す

### 2026-05-02（v0.5.0）

- リポジトリ名を `asw-utils` → `asw-plant` に変更
- プラグイン名を `asw-utils` → `asw-plant` に変更

### 2026-04-24（v0.3.0）

- リポジトリ・プラグイン名を `pov-harness` → `i-harness` に改名
- `role` → `indicator`、`items` → `criteria` に用語統一

### 2026-04-24（v0.2.0）

- チェックリスト駆動評価を導入（`score = 達成基準数 / 全基準数 × 100`）

### 2026-04-23（v0.1.3）

- `default-committee.yml` を廃止。wantree.yml を参照して指標を提案する方式に変更
