---
name: pov-harness
description: |
  This skill should be used when the user asks to "pov-harnessを起動して", "審査委員会を立ち上げて",
  "PoVハーネスを開始して", "観点駆動で開発して",
  or wants to run implementation with multi-perspective review committee evaluation.
  PoV = Point of View（観点）。
version: 0.1.0
tools: Read, Write, Edit, Bash, Agent, AskUserQuestion
---

# pov-harness スキル（準備中）

PoV（観点）駆動の開発ハーネス。

実装プラン完成後に審査委員会（複数のPoVエージェント）を編成し、
実装→多観点評価→修正のループで品質を担保する。

## 現在のステータス

このスキルは現在開発中です。

## 設計概要

```
wantree（要件定義）
    ↓
実装プランニング（プランモード）
    ↓
plan-review（プランレビュー）
    ↓
審査委員会プランニング（テンプレートから編成）
    ↓
審査委員構築（PoVエージェント起動）
    ↓
実装 / 修正  ←────────────────┐
    ↓                          │
審査委員会による並列評価         │
    ↓                          │
審査委員長による集約・判定        │
    ├── 全員100点 → 実装完了     │
    └── 否決あり → 差し戻し ────┘
```
