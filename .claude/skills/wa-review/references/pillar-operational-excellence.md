# 運用上の優秀性 (Operational Excellence)

## 柱の概要

運用上の優秀性は、ソフトウェアを正しく構築し、一貫して優れた顧客体験を提供することへのコミットメントである。
この柱はチームの組織化、ワークロードの設計、大規模な運用、継続的な改善のベストプラクティスを包含する。
ワークロードの状態を可視化し、効果的かつ効率的な運用とイベント対応を実現するアーキテクチャの構築を目指す。

## 設計原則

AWS Well-Architected フレームワーク (2025-02-25) における運用上の優秀性の設計原則は以下の 8 項目である。

1. **ビジネス成果を中心にチームを組織化する** (Organize teams around business outcomes) -- クラウド運用への変革にリーダーシップがコミットし、ビジネスに整合した運用モデルを構築する。
2. **アクショナブルなインサイトのためにオブザーバビリティを実装する** (Implement observability for actionable insights) -- KPI を設定しテレメトリを活用して、ビジネス成果にリスクが生じた場合に迅速に対処する。
3. **可能な限り安全に自動化する** (Safely automate where possible) -- ワークロード全体をコードとして定義し、ガードレール付きで運用を自動化する。
4. **小規模で可逆的な変更を頻繁に行う** (Make frequent, small, reversible changes) -- インクリメンタルな変更で影響範囲を最小化し、迅速なロールバックを可能にする。
5. **運用手順を頻繁に改善する** (Refine operations procedures frequently) -- 定期的なレビューとゲームデーで手順の有効性を確認し、チームに周知する。
6. **障害を予測する** (Anticipate failure) -- 障害シナリオのシミュレーションでリスクプロファイルを把握し、対応手順をテストする。
7. **すべての運用イベントとメトリクスから学ぶ** (Learn from all operational events and metrics) -- 教訓をチームおよび組織全体で共有し、継続的改善を推進する。
8. **マネージドサービスを活用する** (Use managed services) -- AWS マネージドサービスで運用負荷を軽減し、サービスとの連携を中心に手順を構築する。

## チェックポイント

### Organization (組織)

**OPS-1: 運用の優先順位はビジネス成果に基づいて決定されているか**
- ビジネス目標と運用 KPI が整合しているか確認する
- 関連する AWS サービス: AWS Organizations, AWS Control Tower, AWS Well-Architected Tool

**OPS-2: 運用チームの体制と責任範囲は明確に定義されているか**
- 役割・権限・エスカレーションパスが文書化されているか確認する
- 関連する AWS サービス: AWS IAM, AWS IAM Identity Center

**OPS-3: 組織文化は継続的改善を支援しているか**
- ポストモーテムの実施、ナレッジ共有の仕組みがあるか確認する
- 関連する AWS サービス: AWS Well-Architected Tool

### Prepare (準備)

**OPS-4: ワークロードの設計はオブザーバビリティを考慮しているか**
- ログ・メトリクス・トレースが適切に収集され、ダッシュボードで可視化されているか確認する
- 関連する AWS サービス: Amazon CloudWatch, AWS X-Ray, CloudWatch Application Signals

**OPS-5: インフラストラクチャはコードとして管理されているか**
- すべての環境がテンプレートから再現可能で、バージョン管理されているか確認する
- 関連する AWS サービス: AWS CloudFormation, AWS CDK, AWS Service Catalog

**OPS-6: デプロイは自動化され、安全に実行されているか**
- CI/CD パイプライン、自動テスト、ロールバック手順、段階的デプロイが整備されているか確認する
- 関連する AWS サービス: AWS CodePipeline, AWS CodeBuild, AWS CodeDeploy

**OPS-7: 運用準備状況は十分か**
- ランブック・プレイブックが整備され、運用手順が自動化されているか確認する
- 関連する AWS サービス: AWS Systems Manager (Automation, Documents), AWS Chatbot

### Operate (運用)

**OPS-8: ワークロードの健全性は適切に把握されているか**
- リソース・アプリケーション・ビジネスの各メトリクスが収集され、適切なアラートが設定されているか確認する
- 関連する AWS サービス: Amazon CloudWatch (Metrics, Alarms, Dashboards), AWS Health Dashboard

**OPS-9: イベントへの対応は定義され自動化されているか**
- インシデント対応プロセス、自動通知、自動修復アクションが実装されているか確認する
- 関連する AWS サービス: Amazon EventBridge, AWS Systems Manager Incident Manager, Amazon SNS

**OPS-10: 構成管理とパッチ管理は適切に行われているか**
- 構成変更の追跡、パッチ適用プロセス、コンプライアンス評価が行われているか確認する
- 関連する AWS サービス: AWS Config, AWS Systems Manager (Patch Manager, State Manager), AWS CloudTrail

**OPS-11: ログは適切に収集・管理されているか**
- 必要なログの収集、保管期間の定義、検索・分析の仕組みが整備されているか確認する
- 関連する AWS サービス: Amazon CloudWatch Logs, Amazon S3, Amazon OpenSearch Service, AWS CloudTrail

### Evolve (進化)

**OPS-12: 運用の改善は継続的に行われているか**
- 定期的な運用レビュー、トレンド分析、ゲームデーを実施しているか確認する
- 関連する AWS サービス: AWS Well-Architected Tool, AWS Trusted Advisor, AWS Fault Injection Service

**OPS-13: 障害やイベントから得た教訓は組織に共有されているか**
- ポストモーテムの実施と再発防止策の反映、チーム横断での共有がされているか確認する
- 関連する AWS サービス: AWS Systems Manager OpsCenter, AWS Systems Manager Incident Manager

## リポジトリとの対応関係

| チェックポイント | 関連する設計書 | 関連するヒアリング項目 |
|---|---|---|
| OPS-4 (オブザーバビリティ) | cloud-design-monitoring.md | 非機能要件ヒアリング > 監視 |
| OPS-5 (IaC) | cloud-design-deployprovisioning.md | 非機能要件ヒアリング > アプリケーションデプロイ |
| OPS-6 (デプロイ自動化) | cloud-design-deployprovisioning.md | 非機能要件ヒアリング > アプリケーションデプロイ |
| OPS-7 (運用準備) | cloud-design-configurationmanagement.md | 非機能要件ヒアリング > 運用維持管理 |
| OPS-8 (ヘルスモニタリング) | cloud-design-monitoring.md | 非機能要件ヒアリング > 監視 |
| OPS-9 (イベント対応) | cloud-design-monitoring.md | 非機能要件ヒアリング > 監視、運用維持管理 |
| OPS-10 (構成管理・パッチ管理) | cloud-design-configurationmanagement.md<br />cloud-design-patchmanagement.md | 非機能要件ヒアリング > パッチ管理 |
| OPS-11 (ログ管理) | cloud-design-log.md | 非機能要件ヒアリング > ログ管理 |
| OPS-12 (継続的改善) | - | 非機能要件ヒアリング > 運用維持管理 |
| OPS-13 (教訓の共有) | - | 非機能要件ヒアリング > 運用維持管理 |

## よくある指摘事項

### IaC に関する指摘
- IaC を使用せず手動でリソースを構築している。環境間の差異や構成ドリフトの原因となる。
- CloudFormation / Terraform のテンプレートがバージョン管理されていない。
- 一部のリソースだけ IaC 化されており、手動作成のリソースと混在している。

### デプロイに関する指摘
- デプロイが手動で行われており、ヒューマンエラーのリスクがある。
- CI/CD パイプラインが構築されていない、またはテスト工程が含まれていない。
- ロールバック手順が定義されていない、またはテストされていない。
- 本番環境へのデプロイに段階的リリース戦略 (Canary, Blue/Green) が採用されていない。

### 監視・オブザーバビリティに関する指摘
- 監視が CloudWatch のデフォルトメトリクスのみで、カスタムメトリクスやアプリケーションメトリクスが未収集である。
- ビジネス KPI に対応するメトリクスが定義されていない。
- アラートのしきい値が不適切で、アラート疲れが発生している。
- 分散トレーシングが未導入で、マイクロサービス間の問題特定が困難である。

### ログ管理に関する指摘
- ログの保管期間が未定義、またはすべてのログが無期限保管されコストが増大している。
- ログが一元集約されておらず、障害調査に時間がかかる。
- CloudTrail が全リージョンで有効化されていない。

### 運用手順・インシデント管理に関する指摘
- ランブック (定常運用手順書) が存在しない、または最新化されていない。
- プレイブック (障害対応手順書) が存在しない。
- インシデント発生時のエスカレーションパスが定義されていない。
- ポストモーテムが実施されていない、または形骸化している。
- 障害情報やナレッジがチーム内に閉じており、組織横断で共有されていない。

### 構成管理・パッチ管理に関する指摘
- AWS Config が有効化されておらず、構成変更履歴が追跡できない。
- Config Rules によるコンプライアンス評価が行われていない。
- パッチ適用のプロセスとスケジュールが定義されていない。
- Systems Manager Patch Manager を活用したパッチ適用の自動化が行われていない。
