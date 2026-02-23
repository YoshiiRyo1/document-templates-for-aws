# 非機能要件ヒアリングシート 陳腐化チェック結果

2026年2月23日時点で、`survey/doc_source/` 配下の8ファイルに記載されている内容を AWS の最新情報と照合し、陳腐化している記述を特定した。

---

## 重要度: 高（誤り・非推奨）

### 1. RDS SLA 値の誤り — `nfr-reliability.md:9`

**現在の記述:**
> RDS SLA: Single-AZ 99.95%、Multi-AZ 99.99%

**正しい値（2024年1月22日更新）:**
- Multi-AZ: **99.95%**（99.99% ではない）
- Single-AZ: **99.5%**（99.95% ではない）

コンポジット SLA の計算例も修正が必要:
> EC2 Multi-AZ (99.99%) × RDS Multi-AZ (99.99%) = 99.98%

→ 正: EC2 Multi-AZ (99.99%) × RDS Multi-AZ (**99.95%**) = **99.94%**

### 2. Lambda SnapStart の対応言語不足 — `nfr-performance-efficiency.md:26`

**現在の記述:**
> Lambda SnapStart で Java の起動時間を短縮できる。

**最新状況（2024年11月 GA）:** Python 3.12+ と .NET 8+ にも対応済み。Java 限定の記述は古い。

**修正候補:**
> Lambda SnapStart で Java・Python・.NET の起動時間を短縮できる

---

## 重要度: 中（記述が古い・不足）

### 3. Inspector の機能拡大が未反映 — `nfr-security.md:48`

**現在の記述:**
> Amazon Inspector で EC2・Lambda・ECR の脆弱性を自動検出し、SBOM（ソフトウェア部品表）を生成できる。

**不足:** 2025年6月に **Code Security が GA**（SAST・SCA・IaC スキャン対応）。ソースコードと IaC テンプレートの脆弱性スキャンも可能になった。

### 4. GuardDuty の機能拡大が未反映 — `nfr-security.md:48`

**現在の記述:**
> GuardDuty Malware Protection で EC2・ECS・EKS のマルウェアスキャンが可能。

**不足:**
- **Extended Threat Detection**（2025年6月〜12月）: EC2・ECS・EKS でのマルチステージ攻撃検出
- **AWS Backup 統合**（2025年11月）: バックアップのマルウェアスキャン（復旧前に「最後の安全なバックアップ」を特定）

### 5. Security Hub のリスク分析機能が未反映 — `nfr-security.md:8`

**現在の記述:**
> Security Hub の統合ダッシュボードでセキュリティ状態を一元監視できる。

**不足:** 2025年12月に **Near Real-Time Risk Analytics** が GA。攻撃パス可視化、リスク優先順位付け、1年分のトレンド分析が追加された。

### 6. Network Firewall の機能拡大が未反映 — `nfr-security.md:49`

**現在の記述:**
> VPC 内部の IDS/IPS は AWS Network Firewall で実現できる（Suricata 互換ルール対応）。

**不足:**
- **Active Threat Defense**（2025年6月）: AWS 全体で観測される C2 通信・悪意ドメインに自動対応
- **JA4 フィルタリング**（2025年3月）
- **Transit Gateway ネイティブ統合**（2025年7月）: VPC サブネット管理不要に

### 7. IAM Access Analyzer の未記載 — `nfr-security.md`

ID・アクセス管理セクションで IAM Access Analyzer に言及がない。2025年6月に**組織内アクセス検出**機能が追加され、S3・DynamoDB・RDS にアクセス可能なユーザー/ロールを自動特定できるようになった。定期的なアクセス権限レビューの手段として記載すべき。

### 8. AWS Security Incident Response の未記載 — `nfr-security.md`

2025年11月に **agentic AI による自動調査機能**が追加された新サービス。CloudTrail・IAM・EC2 から自動的にエビデンスを収集し、Jira・ServiceNow との双方向同期も可能。インシデント対応計画の質問（`nfr-security.md:18`）で言及を検討。

### 9. Migration Evaluator のリブランド — `nfr-migration.md:7`

**現在の記述:**
> AWS Application Discovery Service や Migration Hub で移行対象を可視化

**最新状況:** AWS Migration Evaluator は **AWS Transform** にリブランドされた（2025年5月）。AI 駆動のインフラ分析とビジネスケース生成を提供。移行対象の棚卸し・評価に関する質問で言及を検討。

### 10. CloudWatch Application Signals の機能拡大が未反映 — `nfr-operational-excellence.md:46`

**不足:**
- **Application Map GA**（2025年10月）: サービス間依存関係の自動発見・可視化
- **非計測サービス検出**（2025年11月）
- **EKS 自動監視**（2025年5月）

### 11. EventBridge のペイロードサイズ拡大が未反映 — `nfr-operational-excellence.md`

2026年1月にイベントペイロード上限が **256KB → 1MB** に拡大。ジョブ管理やイベント駆動設計に影響する。

### 12. Amazon Q Developer の記述が抽象的 — `nfr-operational-excellence.md:16` 付近

**現在の記述:**
> AI 運用支援ツール（Amazon Q Developer）の活用

**不足:** 具体的な活用領域を記載すべき:
- 運用トラブルシューティング（ログ解析、根本原因特定、ランブック推奨）
- セキュリティ調査（コード脆弱性スキャン、IaC 分析）
- コスト分析（自然言語によるコスト照会・最適化提案）
- CloudWatch Alarm からの自動調査トリガー

### 13. Customer Carbon Footprint Tool の Scope 3 対応が未反映 — `nfr-sustainability.md:8`

**現在の記述:**
> AWS Customer Carbon Footprint Tool で AWS 利用に伴う炭素排出量を可視化できる。

**不足:** 2025年10月に **Scope 3 排出データ**が追加された。製造・輸送を含む包括的なカーボンフットプリントが把握可能に。

### 14. AWS Backup の対応範囲拡大が未反映 — `nfr-reliability.md`

**不足:**
- **Amazon EKS サポート**（2025年11月）: クラスタ全体、namespace、個別 PV の復旧
- **GuardDuty Malware Protection 統合**（2025年11月）: 復旧前のマルウェアスキャン
- **単一アクションのクロスリージョン・クロスアカウント DB スナップショットコピー**（2025年10月）

### 15. Cost Optimization Hub の進化が未反映 — `nfr-cost-optimization.md:7`

**不足:**
- **Cost Efficiency メトリクス**（2025年11月）: 最適化進捗の継続追跡
- **ユーザー属性ベースのコスト配分**（2025年12月）: IAM Identity Center のユーザー属性で自動タグ付け
- **Amazon Q Developer によるコスト分析**（2025年11月）: 自然言語での対話的コスト分析

### 16. Systems Manager の新機能が未反映 — `nfr-operational-excellence.md`

**不足:**
- **Node Access Management**（2025年5月）: Slack/Teams 経由での just-in-time ノードアクセス承認
- **Patch Manager セキュリティ更新通知**（2025年10月）: パッチベースラインに不適合だが利用可能なセキュリティ更新の可視化

---

## 重要度: 低（最新機能の追記が望ましい）

### 17. VPC Lattice の機能拡大 — `nfr-infrastructure.md:23`

TCP サポート（2024年12月）、ECS 統合（2024年11月）、IPv6 対応が追加済み。

### 18. Cloud WAN の機能拡大 — `nfr-infrastructure.md`

Routing Policy（2025年11月）、Security Group Referencing（2025年6月）、Direct Connect 統合が追加済み。

### 19. CloudWatch 統合データ管理 — `nfr-operational-excellence.md`

2025年12月に CloudTrail、VPC Flow Logs、Route 53 DNS ログ、WAF ログを OCSF 形式で統一集約する機能が追加。Security Lake だけでなく CloudWatch 側でも OCSF 正規化が可能に。

### 20. Verified Access の機能拡大 — `nfr-security.md:41`

non-HTTP(S) プロトコル対応（2025年2月）、FedRAMP High/Moderate 認可（2025年3月）が追加済み。

---

## 対応方針

上記の指摘事項を Phase 2（既存質問の更新）および Phase 3（新規質問の追加）で反映する。特に重要度「高」の2件は最優先で修正する。
