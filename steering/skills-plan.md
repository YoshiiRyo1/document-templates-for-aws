# Well-Architected レビュースキル作成計画

## Context

このリポジトリは AWS インフラ設計ドキュメントのテンプレート集であり、AWS Well-Architected フレームワークの考え方に準拠している。現在 `.claude/skills/` ディレクトリは空で、スキルは未作成。6 本の柱の専門的視点でアーキテクチャを分析・レビューするスキルを新規作成する。

## スキル概要

| 項目 | 内容 |
|---|---|
| スキル名 | `wa-review` |
| 配置先 | `.claude/skills/wa-review/` |
| 用途 | 設計書・アーキテクチャを 6 本の柱で専門的にレビューし、改善提案を行う |
| トリガー | `/wa-review` またはアーキテクチャレビュー関連の自然言語 |

## ディレクトリ構成

```
.claude/skills/wa-review/
├── SKILL.md                                     # メインスキルファイル
└── references/
    ├── pillar-operational-excellence.md          # 運用上の優秀性
    ├── pillar-security.md                        # セキュリティ
    ├── pillar-reliability.md                     # 信頼性
    ├── pillar-performance-efficiency.md          # パフォーマンス効率
    ├── pillar-cost-optimization.md               # コスト最適化
    ├── pillar-sustainability.md                  # サステナビリティ
    └── output-template.md                        # レビューレポートテンプレート
```

## 実装手順

### Step 1: SKILL.md の作成

**フロントマター:**

- `name: wa-review`
- `description:` 6 本の柱でのレビュー用途と日英トリガーフレーズを記載
- `allowed-tools:` Read, Grep, Glob, aws-knowledge MCP ツール群（読み取り専用）

**本文構成 (~300行):**

1. **ペルソナと前提** - AWS SA としてレビューする前提条件
2. **ワークフロー** - 入力理解 → ドキュメント読み込み → AWS ベストプラクティス確認 → 6 柱分析 → レポート作成
3. **6 本の柱の概要** - 各柱の要点と主な観点（詳細は references/ へ委譲）
4. **入力タイプ別の分析アプローチ** - 設計書レビュー / ヒアリングシートレビュー / アーキテクチャ説明レビュー
5. **出力フォーマット** - 重要度定義（重大/高/中/低）と出力規約
6. **品質ガイドライン** - 具体的指摘、最新情報確認、トレードオフ明示

### Step 2: output-template.md の作成

レビューレポートの Markdown テンプレート:

- レビュー概要テーブル（対象、日付、レビュアー）
- エグゼクティブサマリー + 柱別評価サマリーテーブル
- 柱別レビュー結果（良好な点 + 検出事項テーブル: #/重要度/検出事項/推奨事項/関連サービス）
- 横断的な推奨事項
- 次のアクション（優先順位順）
- 参考リンク

### Step 3: 6 本の柱リファレンスファイルの作成 (各 ~150行)

各ファイルの共通構成:

1. **柱の概要** - 定義と目的
2. **設計原則** - Well-Architected フレームワーク公式の設計原則
3. **チェックポイント** - 柱固有の確認項目（OPS-1〜n, SEC-1〜n 等）
4. **リポジトリとの対応関係** - 既存の設計書・ヒアリング項目とのマッピング
5. **よくある指摘事項** - アンチパターン集

各柱と既存ドキュメントの主な対応:

| 柱 | 対応する設計書 |
|---|---|
| 運用上の優秀性 | deployprovisioning, monitoring, configurationmanagement |
| セキュリティ | security, awsaccount, network, log, patchmanagement |
| 信頼性 | availability, backup, network |
| パフォーマンス効率 | network, monitoring |
| コスト最適化 | （ヒアリングシートのコストセクション） |
| サステナビリティ | （横断的: マネージドサービス活用の観点） |

**重要:** 各柱の設計原則・ベストプラクティスは、作成時に `aws-knowledge` MCP サーバーで最新の Well-Architected フレームワーク情報を確認してから記述する。

## 対象ファイル一覧

| 操作 | ファイルパス |
|---|---|
| 新規作成 | `.claude/skills/wa-review/SKILL.md` |
| 新規作成 | `.claude/skills/wa-review/references/output-template.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-operational-excellence.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-security.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-reliability.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-performance-efficiency.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-cost-optimization.md` |
| 新規作成 | `.claude/skills/wa-review/references/pillar-sustainability.md` |

既存ファイルの変更は不要。

## 参照すべき既存リソース

- [CLAUDE.md](../CLAUDE.md) - Markdown スタイル規約、ペルソナ定義
- [survey/doc_source/non-functional-requirement.md](../survey/doc_source/non-functional-requirement.md) - ヒアリング項目の形式・内容参考
- [design/README.md](../design/README.md) - 設計書ファイル一覧

## 検証方法

1. `/wa-review` コマンドでスキルが呼び出されることを確認
2. 既存の設計書（例: `design/doc_source/cloud-design-security.md`）を対象にレビューを実行し、6 柱すべてについて具体的な指摘が出力されることを確認
3. 出力が `output-template.md` のフォーマットに準拠していることを確認
4. aws-knowledge MCP サーバーが利用されていることを確認
