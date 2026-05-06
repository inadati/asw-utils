---
name: crucible-harness
description: |
  This skill should be used when the user wants to "crucible-harnessを起動して",
  "crucible-harnessをはじめて", "crucible-harnessのセットアップをして",
  "crucible-harnessで品質を改善したい",
  or wants to start the crucible-harness preparation phase.
  Runs Phase 0: task definition, checklist creation, parameter setup.
  After completion, hand off to /crucible-harness:run.
version: 0.1.0
tools: AskUserQuestion, Write, Read
---

# crucible-harness セットアップスキル

## 概要

crucible-harness の Phase 0（セットアップ）を担当する。
タスク定義・チェックリスト・閾値・最大反復回数を対話的に設定し、
`.crucible/config.md` に保存して `/crucible-harness:run` に引き継ぐ。

## 実行手順

### Step 1: タスク定義の確認

`AskUserQuestion` でタスクの内容を確認する。

### Step 2: チェックリストの提案

タスク定義をもとに、評価チェックリストを提案する。
5〜8項目を目安に、タスクの品質を多角的に評価できる基準を提案すること。

提案フォーマット:
```
以下のチェックリストを提案します。修正・追加・削除があればお知らせください。

1. [評価項目名]: [評価観点の説明]
2. [評価項目名]: [評価観点の説明]
...
```

### Step 3: チェックリストの承認

ユーザーの修正指示を反映し、`AskUserQuestion` で最終承認を得る。

- 承認 → 確定
- 修正指示があれば反映して再提示

### Step 4: パラメータ設定

`AskUserQuestion` で以下を確認する（Enterでデフォルト値を使用）:

- 最低点フロア: 各項目の最低スコア（デフォルト: 0.5）
- 平均閾値: 全項目の平均スコア（デフォルト: 0.75）
- 最大反復回数（デフォルト: 5）

### Step 5: 設定ファイルの保存

`Write` ツールで `.crucible/config.md` を以下の形式で保存する:

```markdown
# crucible-harness 設定

## タスク定義
[ユーザーが定義したタスクの内容]

## タスク種別
[タスク定義から推定したタスク種別（例: ブログ記事・コード実装・資料作成）]

## チェックリスト
1. [項目名]: [評価観点]
2. [項目名]: [評価観点]
...

## パラメータ
- 最低点フロア: [値]
- 平均閾値: [値]
- 最大反復回数: [値]
```

### Step 6: 完了通知

セットアップ完了を伝え、`/crucible-harness:run` の起動を案内する。

## 注意事項

- チェックリストはAIが提案し人間が承認する。独断で確定しない
- `.crucible/` ディレクトリはプロジェクトルートに作成する
- 既存の `.crucible/config.md` がある場合は上書き前にユーザーに確認する
