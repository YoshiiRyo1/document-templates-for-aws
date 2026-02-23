# 非機能要件ヒアリングシート 2026年版 更新計画

## Context

`survey/doc_source/non-functional-requirement.md` は2020年7月に作成され、約95問で構成されている。
2021年に Well-Architected Framework に Sustainability 柱が追加され、2024-2025年にかけて全78ベストプラクティスが刷新された。
また、Security Lake、Resilience Hub、Verified Access、CloudWatch Application Signals 等の主要サービスが2020年以降にリリースされている。
これらの変化を取り込み、性能・拡張性の深掘り、業界固有コンプライアンス要件、移行要件を追加する。

## ファイル構成

**W-A 6 pillars + 環境 + マイグレーション + 生成 AI の9ファイルに分割する。** 役割別のヒアリング、段階的な顧客提供、Git 差分管理の容易さを重視する。旧 `non-functional-requirement.md` は削除する。

### 新規ファイル一覧 (`survey/doc_source/`)

| #   | ファイル名                      | 内容                                                                       | 問数 |
| --- | ------------------------------- | -------------------------------------------------------------------------- | ---- |
| 1   | `nfr-reliability.md`            | 信頼性（稼働率、バックアップ、災害対策）                                   | 23問 |
| 2   | `nfr-security.md`               | セキュリティ（コンプライアンス、データ保護、ID管理、攻撃防御、パッチ管理） | 45問 |
| 3   | `nfr-operational-excellence.md` | 運用上の優秀性（運用体制、ジョブ、ログ、監視、デプロイ）                   | 44問 |
| 4   | `nfr-performance-efficiency.md` | パフォーマンス効率（性能目標、拡張性、キャッシュ、テスト）                 | 21問 |
| 5   | `nfr-cost-optimization.md`      | コスト最適化                                                               | 17問 |
| 6   | `nfr-sustainability.md`         | サステナビリティ                                                           | 5問  |
| 7   | `nfr-infrastructure.md`         | 環境（AWSアカウント、ネットワーク、ドメイン、ライセンス）                  | 15問 |
| 8   | `nfr-migration.md`              | マイグレーション                                                           | 15問 |
| 9   | `nfr-generative-ai.md`          | 生成 AI（ガバナンス、セキュリティ、運用、パフォーマンス、コスト）          | 7問  |

### その他の更新対象

- `survey/README.md` — ファイル一覧テーブルを9ファイル構成に書き換え
- `survey/doc_source/non-functional-requirement.md` — 削除

---

## 更新後のドキュメント構造

現行の構造を W-A Framework 6本の柱に沿って再編し、新規セクションを追加する。

```
# 非機能要件ヒアリング

## 信頼性 (Reliability)                     ← 旧「可用性」を改称           [23問]
  ### 稼働率                                  (4→5問)
  ### バックアップ / リストア                 (7→10問)
  ### 災害対策                                (5→8問)

## セキュリティ (Security)                   ← 旧セクションを統合・再編     [45問]
  ### コンプライアンス・規制対応              (9→15問) ※業界別対応を大幅追加
  ### データ保護                              (新設 5問) ※旧コンプライアンスから分離
  ### ID・アクセス管理                        (旧4→9問) ※旧「運用アカウント管理」を移動
  ### 攻撃防御                                (4→8問)
  ### パッチ管理                              (8→8問) ※質問数維持、内容更新

## 運用上の優秀性 (Operational Excellence)    ← 旧「運用」を改称             [44問]
  ### 運用体制・運用維持管理                  (8→10問)
  ### ジョブ管理                              (3→4問)
  ### ログ管理                                (4→7問)
  ### 監視・オブザーバビリティ                (10→17問) ※旧「監視」を改称
  ### アプリケーションデプロイ                (5→7問)

## パフォーマンス効率 (Performance Efficiency) ← 旧「キャパシティ」を改称     [21問]
  ### 性能目標値                              (4→8問)
  ### 拡張性・スケーリング                    (5→8問)
  ### キャッシュ戦略                          (新設 3問)
  ### テスト要件                              (新設 3問)

## コスト最適化 (Cost Optimization)           ← 旧「コスト」を改称           [17問]
  (9→13問)

## サステナビリティ (Sustainability)          ← 完全新規                     [5問]
  (新設 5問)

## 環境 (Infrastructure)                                                     [15問]
  ### AWS アカウント                          (3→6問)
  ### ネットワークトポロジ                    (6→9問)
  ### ドメイン                                (2→2問)
  ### ライセンス管理                          (2→2問)

## マイグレーション (Migration)               ← 完全新規                     [15問]
  (新設 14問)

## 生成 AI (Generative AI)                   ← 完全新規                     [7問]
  ### ガバナンス・データ保護                  (新設 2問)
  ### セキュリティ                            (新設 1問)
  ### 運用・オブザーバビリティ                (新設 1問)
  ### パフォーマンス・コスト                  (新設 3問)
```

---

## セクション別の変更内容

### 1. 信頼性 (Reliability)

**稼働率 (4→5問)**

- UPDATE: Q3 の SLA 値を最新に更新、マルチリージョン設計目標を追記
- ADD: コンポジット SLA（複合 SLA）の考え方を確認する質問

**バックアップ / リストア (7→10問)**

- UPDATE: Q1, Q5 の質問の意図に AWS Backup 一元管理、Elastic Disaster Recovery を追加
- ADD: クロスアカウント・クロスリージョンバックアップコピーの要否
- ADD: イミュータブルバックアップ（Backup Vault Lock）の要否（ランサムウェア対策）
- ADD: 論理的エアギャップ保護（Logically Air-Gapped Vault + Multi-party Approval）

**災害対策 (5→8問)**

- UPDATE: Q1 に DRS、Route 53 ARC の選択肢追加。Q2 にサイバー攻撃を災害定義に追加
- ADD: DR 戦略パターン選択（Backup & Restore / Pilot Light / Warm Standby / Multi-Site Active-Active）
- ADD: レジリエンステスト・カオスエンジニアリング（AWS FIS、Resilience Hub）の実施要否
- ADD: DR 訓練の頻度・体制

### 2. セキュリティ (Security)

旧「可用性」配下のコンプライアンス、パッチ管理、攻撃防御、運用アカウント管理を統合・再編する。

**コンプライアンス・規制対応 (9→15問)**

- UPDATE: Q2 に Audit Manager、Security Hub 統合ダッシュボードを反映
- MOVE: Q7-Q9（データ保護関連）→「データ保護」サブセクションへ移動
- MOVE: Q4-Q5（MFA/パスワード）→「ID・アクセス管理」へ移動
- ADD: 準拠必要なフレームワーク（PCI DSS, HIPAA, SOC 2, GDPR, FISC, ISMAP）
- ADD: データレジデンシー要件
- ADD: 監査要件と頻度（Audit Manager）
- ADD: 個人情報保護法・プライバシー規制対応（Amazon Macie）
- ADD: データ機密分類レベルの定義
- ADD: Organizations リソース制御ポリシー（RCP）— SCP を補完するリソース側アクセス制御

**データ保護 (新設 5問)**

- MOVE: 旧コンプライアンス Q7（保管データ分類）に Macie 追記
- MOVE: 旧コンプライアンス Q8（転送中データ）に TLS 1.2 以上必須化を更新
- MOVE: 旧コンプライアンス Q9（暗号化キー）に KMS マルチリージョンキー、外部キーストア (XKS) 追記
- ADD: セキュリティデータ集約・分析基盤（Security Lake / OCSF）
- ADD: データライフサイクル管理ポリシー

**ID・アクセス管理 (旧4→9問)**

- MOVE+UPDATE: 旧「運用アカウント管理」4問を IAM Identity Center ベースに更新
- MOVE: 旧コンプライアンス Q4（MFA）、Q5（パスワードポリシー）
- ADD: Zero Trust アーキテクチャ（Verified Access）
- ADD: 外部 IdP 連携（SAML/SCIM）
- ADD: 特権アクセス管理
- ADD: アプリケーションレベル認可制御（Amazon Verified Permissions / Cedar ポリシー言語）
- ADD: IAM ルートユーザー一元管理（Central Management for Root Access）

**攻撃防御 (4→8問)**

- UPDATE: Q1 に GuardDuty Malware Protection、Inspector SBOM 追加
- UPDATE: Q2 に Network Firewall、Route 53 Resolver DNS Firewall 追加
- ADD: DDoS 対策（Shield Standard / Advanced）
- ADD: ボット対策（WAF Bot Control）
- ADD: SIEM 要件（Security Lake + OpenSearch or 3rd party）
- ADD: VPC Block Public Access（IGW 経由インターネットアクセスの一括制御）

**パッチ管理 (8→8問、質問数維持)**

- UPDATE: Q3 に Patch Manager 自動適用・コンプライアンスレポート反映
- UPDATE: Q5 に Inspector 自動脆弱性検出追加

### 3. 運用上の優秀性 (Operational Excellence)

**運用体制・運用維持管理 (8→10問)**

- UPDATE: Q1 に Platform Engineering チーム追加。Q8 に EventBridge + Lambda 自動復旧追記
- ADD: IaC 採用方針（CloudFormation, CDK, Terraform）
- ADD: AI 運用支援ツール（Amazon Q Developer）の活用

**ジョブ管理 (3→4問)**

- UPDATE: Q1 の「CloudWatch Events」→「EventBridge」。EventBridge Scheduler 追加
- ADD: サーバーレスワークフロー（Step Functions, EventBridge Pipes）

**ログ管理 (4→7問)**

- UPDATE: Q1 の「Elasticsearch Service」→「OpenSearch Service」、コンテナログ追加
- UPDATE: Q4 に CloudWatch Logs Insights 強化、Security Lake + Athena 追記
- ADD: ログ集約・正規化（Security Lake / OCSF）
- ADD: マルチアカウントログ集約
- ADD: CloudTrail Lake によるアクティビティログの長期保管・SQL 分析（AI パワード Highlights）

**監視・オブザーバビリティ (10→17問)**

- UPDATE: Q3 の「PHD」→「AWS Health Dashboard」
- UPDATE: Q5 に CloudWatch Application Signals、X-Ray 追加
- UPDATE: インシデント対応フロー — Incident Manager（メンテナンスモード移行）→ CloudWatch Investigations（AI パワード根本原因分析、5 Whys 自動生成）に更新
- ADD: オブザーバビリティ3本柱（メトリクス・ログ・トレース）統合管理
- ADD: エンドユーザー体験監視（Internet Monitor）
- ADD: インシデント対応フロー（CloudWatch Investigations）
- ADD: SLO/SLI 定義（Application Signals SLO、バーンレートアラート、エラーバジェット）
- ADD: データベースオブザーバビリティ（CloudWatch Database Insights）
- ADD: OSS ベースのオブザーバビリティツール（Managed Grafana / Managed Service for Prometheus）

**アプリケーションデプロイ (5→7問)**

- UPDATE: Q1-Q2 に CodePipeline V2、ECR/ECS パイプライン追記
- ADD: デプロイ戦略（Blue/Green, Canary, Rolling）
- ADD: インフラ CI/CD（GitOps / IaC パイプライン）

### 4. パフォーマンス効率 (Performance Efficiency)

**性能目標値 (4→8問)**

- KEEP: 既存4問維持
- ADD: パーセンタイルベースのレイテンシ要件（P50/P95/P99）
- ADD: 同時接続数・スループット（リクエスト/秒）目標
- ADD: DB 読み書き比率・クエリパターン（Read Replica, ElastiCache, Aurora Serverless v2）
- ADD: API レートリミット・スロットリング要件

**拡張性・スケーリング (5→8問)**

- UPDATE: Q2 に Aurora Serverless v2、ElastiCache Serverless、Lambda SnapStart、App Runner 追記
- ADD: グローバルパフォーマンス要件（CloudFront, Global Accelerator, S3 Express One Zone）
- ADD: コンテナ/サーバーレス採用方針（ECS, EKS, App Runner, Lambda）
- ADD: マルチリージョンデータベース要件（Aurora DSQL — サーバーレス分散 SQL / 99.999% 可用性、Aurora Global Database、DynamoDB Global Tables）

**キャッシュ戦略 (新設 3問)**

- キャッシュ活用要件（ElastiCache, DAX, API Gateway キャッシュ）
- キャッシュ無効化ポリシー・TTL 要件
- CDN（CloudFront）活用要件

**テスト要件 (新設 3問)**

- 負荷テスト・ストレステスト・耐久テスト実施要件
- テスト環境構築方針（本番同等 / 縮小 / オンデマンド）
- パフォーマンスベースライン定義

### 5. コスト最適化 (9→13問)

- UPDATE: Q2 に Budgets Actions、Cost Anomaly Detection 追加
- UPDATE: Q3 の「CUR」→「Data Exports (CUR 2.0)」、Cost Optimization Hub 追記
- ADD: コスト配分タグ戦略
- ADD: コンピューティング購入方針（オンデマンド / RI / Savings Plans / Spot）
- ADD: FinOps 体制・プロセス
- ADD: コンテナワークロードのコスト配分（Split Cost Allocation Data for EKS — コンテナレベル CPU/メモリ/GPU 按分、Kubernetes ラベルインポート）

### 6. サステナビリティ (新設 5問)

- リージョン選択での環境配慮（再生可能エネルギー比率）
- リソース効率化方針（Graviton、サーバーレス）
- 不要データの削除・アーカイブポリシー
- カーボンフットプリント可視化（Customer Carbon Footprint Tool）
- 組織のサステナビリティ目標・KPI

### 7. 環境 (Infrastructure)

**AWS アカウント (3→6問)**

- UPDATE: Q3 に Control Tower、Organizations SCP 追記
- ADD: マルチアカウント戦略 / Landing Zone 採用（Control Tower）
- ADD: ガバナンス要件（予防的/検出的ガードレール）
- ADD: Organizations 宣言型ポリシー（Declarative Policies）— サービス設定の宣言的強制（パブリック AMI 共有ブロック、インスタンスタイプ制限、VPC 設定制限等）

**ネットワークトポロジ (6→9問)**

- UPDATE: Q2 に Cloud WAN、Transit Gateway 明示
- ADD: Cloud WAN / VPC Lattice 要件
- ADD: PrivateLink 要件
- ADD: IP アドレスの一元管理（VPC IPAM — 組織全体の IP アドレス計画・割当・追跡、BYOIP 対応）

**ドメイン (2→2問、維持)**

- UPDATE: Q2 の ACM 仕様を ECDSA 対応等に更新

**ライセンス管理 (2→2問、変更なし)**

### 8. マイグレーション (新設 12問 → 14問)

冒頭に「移行が不要な場合はスキップ」の旨を記載。

- UPDATE: 移行対象の棚卸し — Application Discovery Service / Migration Hub（メンテナンスモード移行）→ AWS Transform（AI 駆動インフラ分析、依存関係マッピング、ビジネスケース生成）に更新
- 移行戦略（7Rs: Rehost / Replatform / Refactor / Repurchase / Relocate / Retain / Retire）
- データ移行量・方式（DMS, DataSync, Snow Family, Transfer Family）
- カットオーバー方式（ビッグバン / 段階的）
- 並行運用期間
- ロールバック計画・判断基準
- DNS / ネットワーク切り替え方式
- 移行後の検証・受入テスト
- 許容ダウンタイム（DMS CDC, DRS 選定に影響）
- スケジュール・マイルストーン
- 既存環境廃止計画
- ライセンス変更影響（License Manager）
- ADD: AI を活用した移行計画・実行の自動化（AWS Transform — VMware 大規模移行、.NET モダナイゼーション支援）
- ADD: レガシーコードの近代化（AWS Transform custom — Java/Python/Node.js ランタイム更新、Amazon Q Developer Code Transformation）

### 9. 生成 AI (Generative AI) — 完全新規 (7問)

生成 AI ワークロードの非機能要件を集約する専用ファイル `nfr-generative-ai.md` を新規作成する。

**ガバナンス・データ保護 (新設 2問)**

- ADD: 生成 AI の利用に関するガバナンスポリシー（Responsible AI、Bedrock Guardrails — コンテンツフィルタリング / PII マスキング / Automated Reasoning checks）
- ADD: 生成 AI のデータ保護方針（Bedrock データ非学習保証、PrivateLink アクセス、Invocation Logging）

**セキュリティ (新設 1問)**

- ADD: AI/ML ワークロード固有のセキュリティ対策（プロンプトインジェクション / ジェイルブレイク / データポイズニング対策、Guardrails 攻撃防御、AgentCore Gateway Policy）

**運用・オブザーバビリティ (新設 1問)**

- ADD: 生成 AI アプリケーションの運用管理（AgentCore — エージェントデプロイ / スケーリング / オブザーバビリティ、CloudWatch 生成 AI オブザーバビリティ）

**パフォーマンス・コスト (新設 3問)**

- ADD: 生成 AI 推論のパフォーマンス要件（TTFT / TPS / E2E Latency、プロビジョンドスループット、モデル選択とレイテンシ）
- ADD: 生成 AI ワークロードのコスト管理（オンデマンド vs プロビジョンドスループット、プロンプトキャッシング、バッチ推論）
- ADD: 生成 AI アプリケーションの品質評価・テスト（Bedrock Model Evaluation、Automated Reasoning checks、継続的評価パイプライン）

> **サービスメンテナンス移行に関する注記**: 2025年11月に AWS Systems Manager Incident Manager、AWS Application Discovery Service、AWS Migration Hub がメンテナンスモードへの移行を発表した。本更新では該当する既存質問を CloudWatch Investigations（インシデント対応）、AWS Transform（移行棚卸し）に更新している。

---

## 実装手順

### Phase 1: 8ファイルの作成と既存質問の振り分け （実施済み）

- 8つの新規ファイルを `survey/doc_source/` に作成
- 旧 `non-functional-requirement.md` から既存質問を各ファイルに振り分け（内容はまだ変更しない）
- 旧「可用性」配下のセキュリティ関連を `nfr-security.md` に移動
- 旧「運用アカウント管理」を `nfr-security.md` の「ID・アクセス管理」に移動
- 各ファイルの見出し構造（`#` タイトル、`##` サブセクション）を整える

### Phase 2: 既存質問の更新 (~25問) （実施済み）

- AWS サービス名更新（PHD → Health Dashboard, ES → OpenSearch 等）
- SLA 値や技術仕様の更新
- 質問の意図に新サービスの選択肢を追記
- aws-knowledge MCP で最新情報を確認しながら実施

### Phase 3a: 新規質問の追加 (~60問) （実施済み）

- 既存ファイルへの新規質問追加
- `nfr-sustainability.md` の新規作成（完全新規）
- `nfr-migration.md` の新規作成（完全新規）
- キャッシュ戦略・テスト要件サブセクションの追加

### Phase 3b: 2024-2026 AWS アップデート反映 (+22問)

2024年後半〜2026年にかけての AWS 主要アップデートを反映する追加質問の作成。

- `nfr-generative-ai.md` の新規作成（7問）— 生成 AI ワークロードの非機能要件
- 既存8ファイルへの新規質問追加（15問）
  - nfr-reliability.md (+1): 論理的エアギャップ保護
  - nfr-security.md (+4): RCP、Verified Permissions、ルートユーザー一元管理、VPC Block Public Access
  - nfr-operational-excellence.md (+4): CloudTrail Lake、Database Insights、SLO/SLI、OSS オブザーバビリティ
  - nfr-performance-efficiency.md (+1): マルチリージョンデータベース
  - nfr-cost-optimization.md (+1): コンテナコスト配分
  - nfr-infrastructure.md (+2): 宣言型ポリシー、VPC IPAM
  - nfr-migration.md (+2): AI 移行自動化、レガシーコード近代化
- 既存質問の更新（2箇所）
  - Incident Manager → CloudWatch Investigations（メンテナンスモード移行対応）
  - Application Discovery Service / Migration Hub → AWS Transform（メンテナンスモード移行対応）
- aws-knowledge MCP で各質問の意図に記載した AWS サービス情報の最新仕様を最終確認

### Phase 4: レビュー・品質確認

- 記述規約の確認（文末「。」+ 半角スペース2つ、`<br />` セル内改行）
- 質問文の平易さ確認
- テーブルフォーマット統一
- `survey/README.md` を9ファイル構成に書き換え
- 全ファイルの合計質問数が 192 問であることを確認

---

## スコープ見積もり

| 項目        | 現行（Phase 3a 完了時） | Phase 3b 追加 | 最終 |
| ----------- | ---------------------- | ------------- | ---- |
| ファイル数  | 8                      | +1            | 9    |
| 小分類 (##) | 22                     | +4            | 26   |
| 質問数      | 170                    | +22           | 192  |

### Phase 3b ファイル別内訳

| ファイル | Phase 3a 完了時 | 追加 | 最終 |
|---------|----------------|------|------|
| nfr-generative-ai.md | - | **+7** | **7** |
| nfr-reliability.md | 22 | +1 | 23 |
| nfr-security.md | 41 | +4 | 45 |
| nfr-operational-excellence.md | 40 | +4 | 44 |
| nfr-performance-efficiency.md | 20 | +1 | 21 |
| nfr-cost-optimization.md | 16 | +1 | 17 |
| nfr-sustainability.md | 5 | 0 | 5 |
| nfr-infrastructure.md | 13 | +2 | 15 |
| nfr-migration.md | 13 | +2 | 15 |
| **合計** | **170** | **+22** | **192** |

---

## 検証方法

1. Markdown の構文チェック: 各ファイルのテーブル記法の整合性、見出しレベルの統一を確認
2. 記述規約の準拠: CLAUDE.md に定義されたスタイル（文末の「。」+ 半角スペース2つ、`<br />` セル内改行、プレースホルダー）を確認
3. AWS 情報の正確性: aws-knowledge MCP サーバーでサービス名・機能の最新仕様を確認
4. ファイル網羅性: 旧ファイルの全質問が9ファイルのいずれかに含まれていることを確認（漏れチェック）
5. 合計質問数: 全9ファイルの質問数合計が 192 問であることを確認
