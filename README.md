# i-harness

指標（indicator）駆動の開発ハーネス。
i = indicator（指標）。

要件定義・プランレビュー・多指標審査団を統合した Claude Code 開発支援プラグイン。

## スキル一覧

| スキル | 呼び出し | 概要 |
|--------|---------|------|
| `wantree` | `/i-harness:wantree` | ヒアリング形式で要件定義YAMLを作成・管理 |
| `plan-review` | AUTO-TRIGGER のみ | プランを最小決定事項に分解してステップバイステップ確認 |
| `i-harness` | `/i-harness:i-harness` | 指標駆動の審査団による多指標評価 |

## インストール

```
/plugin install https://github.com/inadati/i-harness
```

## ライセンス

MIT
