---
name: nfr-outdated-check
description: This skill should be used when the user asks to "check outdated", "outdated check", "陳腐化チェック", "最新化チェック", or "NFR update check" for the non-functional requirement hearing sheets.
allowed-tools: Read, Grep, Glob, mcp__aws-knowledge, Write, Edit
---

# 非機能要件ヒアリングシート 陳腐化チェック

`survey/doc_source/nfr-*.md` に記載されている AWS サービスの仕様・機能・ベストプラクティスを AWS の最新情報と照合し、陳腐化している記述を特定するスキル。

## 前提

- AWS ソリューションアーキテクトのペルソナで振る舞う。
- 本リポジトリのディレクトリ構成を前提とする。
  - `survey/doc_source/nfr-*.md` - 非機能要件ヒアリングシート（8ファイル）
  - `.claude/references/nfr-taxonomy.md` - 非機能要件カテゴリ定義
- 出力はすべて日本語で記述する。AWS サービス名・技術用語は英語表記のまま使用する。
- aws-knowledge MCP サーバーで AWS の最新仕様・ベストプラクティスを確認してから判定する。推測や古い知識に基づく指摘を避ける。

## チェック対象ファイル

| # | ファイル | カテゴリ |
|---|---------|---------|
| 1 | `nfr-reliability.md` | 信頼性（稼働率、バックアップ、災害対策） |
| 2 | `nfr-performance-efficiency.md` | パフォーマンス効率（性能目標、拡張性、キャッシュ、テスト） |
| 3 | `nfr-security.md` | セキュリティ（コンプライアンス、データ保護、ID管理、攻撃防御、パッチ管理） |
| 4 | `nfr-operational-excellence.md` | 運用上の優秀性（運用体制、ジョブ、ログ、監視、デプロイ） |
| 5 | `nfr-migration.md` | マイグレーション |
| 6 | `nfr-cost-optimization.md` | コスト最適化 |
| 7 | `nfr-sustainability.md` | サステナビリティ |
| 8 | `nfr-infrastructure.md` | 環境（AWSアカウント、ネットワーク、ドメイン、ライセンス） |

## ワークフロー

### Step 1: 入力の理解

`$ARGUMENTS` でチェック範囲を指定できる。

- 引数なし: 全8ファイルをチェック
- ファイル名指定（例: `nfr-security.md`）: 指定ファイルのみチェック
- カテゴリ指定（例: `セキュリティ`）: 該当カテゴリのファイルのみチェック

### Step 2: 対象ファイルの読み込み

非機能要件カテゴリ定義とチェック対象ファイルを読み込む。

```
Read: .claude/references/nfr-taxonomy.md
Glob: survey/doc_source/nfr-*.md
Read: 各対象ファイル
```

### Step 3: AWS サービス情報の抽出

各ファイルから以下の情報を抽出する。

- AWS サービス名（例: RDS, Lambda, GuardDuty）
- SLA・クォータ等の数値（例: 99.95%, 256KB）
- サービスの機能・対応範囲の記述（例: 「Java の起動時間を短縮」）
- 非推奨・リブランド済みサービスの記述（例: Migration Evaluator）
- ベストプラクティスの記述

### Step 4: AWS 最新情報との照合

抽出した情報を aws-knowledge MCP サーバーで照合する。

**照合の観点:**

1. **SLA・クォータの正確性**: 記載されている数値が最新の公式値と一致するか
   ```
   aws___search_documentation: "{サービス名} SLA" を reference_documentation で検索
   ```

2. **サービス機能の最新性**: 記載されている機能が最新の対応状況を反映しているか
   ```
   aws___search_documentation: "{サービス名} new features" を current_awareness で検索
   ```

3. **サービス名のリブランド**: 旧名称のまま記載されていないか
   ```
   aws___search_documentation: "{旧サービス名}" を current_awareness で検索
   ```

4. **新サービス・新機能の欠落**: 記載すべき新サービスや新機能が言及されていないか
   ```
   aws___search_documentation: "{カテゴリ} best practices" を general で検索
   ```

5. **非推奨サービスの記載**: 非推奨や提供終了になったサービスが残っていないか

**照合の注意事項:**

- aws-knowledge MCP の検索結果で確認できた情報のみを指摘の根拠とする。
- 確認できなかった場合は指摘しない。不確実な指摘は信頼性を損なう。
- ファイルごとに網羅的に確認する。特定のサービスだけでなく、記載されているすべての AWS サービスを照合対象とする。

### Step 5: 重要度の判定

各指摘事項に重要度を付与する。

| 重要度 | 定義 | 例 |
|--------|------|-----|
| 高（誤り・非推奨） | SLA 値の誤り、非推奨サービスの記載、対応言語・対応範囲の誤り | RDS Multi-AZ SLA が 99.99% と記載（正: 99.95%） |
| 中（記述が古い・不足） | 新機能・新サービスの追記が必要、リブランドの未反映 | GuardDuty の新機能が未反映 |
| 低（最新機能の追記が望ましい） | あれば望ましいが、現在の記述が間違っているわけではない | VPC Lattice の TCP 対応追加 |

### Step 6: レポート作成

分析結果を出力テンプレートに従ってレポートにまとめる。

```
Read: references/output-template.md
```

レポートの保存先: `steering/nfr-outdated-check-{YYYYMMDD}.md`

日付は実行日を使用する。

## 出力規約

- 文末は `。` で終え、末尾に半角スペース 2 つを付与して改行する。
- AWS サービス名・技術用語は英語表記のまま使用する。
- 指摘事項にはファイル名と該当行番号を付記する（例: `nfr-reliability.md:9`）。
- 「現在の記述」と「正しい値」または「最新状況」を明示し、差分を明確にする。
- 修正候補がある場合は具体的な修正文を提示する。

## 品質ガイドライン

- **正確性最優先**: aws-knowledge MCP で確認できない情報は指摘しない。「おそらく古い」という推測に基づく指摘を避ける。
- **再現性**: 同じファイルに対して同じチェックを実行すれば同じ結果になるよう、チェック基準を明確にする。
- **実行可能性**: 指摘事項には具体的な修正候補を含め、修正作業に直接着手できるようにする。
- **網羅性**: チェック対象ファイルに記載されているすべての AWS サービスを照合対象とする。特定のサービスに偏らない。
- **ノイズの排除**: 記述が古いが実害がないもの（例: 機能名の表記揺れ）は重要度「低」とし、重要な指摘が埋もれないようにする。
