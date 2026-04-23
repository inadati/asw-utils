# pov-harness

PoV（観点）駆動の開発ハーネス。
PoV = Point of View（観点）。

要件定義・プランレビュー・多観点審査委員会を統合した Claude Code 開発支援プラグイン。

## スキル一覧

| スキル | 呼び出し | 概要 |
|--------|---------|------|
| `wantree` | `/pov-harness:wantree` | ヒアリング形式で要件定義YAMLを作成・管理 |
| `plan-review` | `/pov-harness:plan-review` | プランを最小決定事項に分解してステップバイステップ確認 |
| `pov-harness` | `/pov-harness:pov-harness` | PoV駆動の審査委員会による多観点評価（開発中） |

## インストール

```
/plugin install https://github.com/inadati/pov-harness
```

## ライセンス

MIT
