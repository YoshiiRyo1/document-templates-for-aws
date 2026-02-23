# 変更履歴

## 2026-02-23

### 非機能要件ヒアリングシートの大規模リニューアル

- 非機能要件ヒアリングシートを7カテゴリに分割して再構成
  - `nfr-reliability.md`（可用性）
  - `nfr-performance-efficiency.md`（性能・拡張性）
  - `nfr-operational-excellence.md`（運用・保守性）
  - `nfr-migration.md`（移行性）
  - `nfr-security.md`（セキュリティ）
  - `nfr-sustainability.md`（環境・エコロジー）
  - `nfr-cost-optimization.md`（コスト最適化）
  - `nfr-infrastructure.md`（インフラ基盤共通）
  - `nfr-generative-ai.md`（生成 AI）
- 旧 `non-functional-requirement.md` を廃止し、カテゴリ別ファイルへ移行
- 2024〜2026 年の AWS アップデートを反映し、22 問を追加・更新
- 陳腐化チェック用スキル（`nfr-outdated-check`）を追加

### Claude Code の導入・設定

- `CLAUDE.md`（プロジェクト指示書）を作成
- `.mcp.json`（MCP サーバー設定）を追加
- `.claude/settings.json`（権限設定）を追加
- `.gitignore` を追加
- 非機能要件の定義体系（IPA + Well-Architected ハイブリッド 7 カテゴリ）を策定し `.claude/references/nfr-taxonomy.md` に記載
- `wa-review` スキル（Well-Architected レビュー支援）を追加

## 2023-12-12

- 非機能要件ヒアリングシートにコスト関連の質問を追加

## 2023-02-27

- `design/README.md` にリンクと説明を追加

## 2022-05-16

- 非機能要件ヒアリングシートを更新

## 2021-11-30

- 非機能要件ヒアリングシートに拡張性項目（DNS 名でのアクセス対応確認）を追加（PR #5）

## 2021-11-15

- 非機能要件ヒアリングシートの typo 修正

## 2021-07-23

- 監視設計シートを更新

## 2021-07-14

- 非機能要件ヒアリングシートに DR 切り替え判断の項目を追加

## 2021-07-07

- 非機能要件ヒアリングシートの転送中データ暗号化の文言を修正

## 2021-06-28

- 非機能要件ヒアリングシートの稼働率・可用性の設計目標を Well-Architected の内容に合わせて修正
- 非機能要件ヒアリングシートに DR 条件を追加
- 改行の重複・typo 修正

## 2021-03-31

- 非機能要件ヒアリングシートに接続元・接続先の制限項目を追加

## 2021-03-23

- 監視設計シートに各アラートと対応手順のセクションを追加

## 2020-10-20

- ネットワーク設計シートにタイムサーバーの説明を追加
- マルチアカウントワークショップ資料を更新
- ネットワークワークショップ資料を更新

## 2020-10-08

- 非機能要件ヒアリングシートを更新

## 2020-09-08

- 監視設計シートを更新

## 2020-08-26 〜 2020-08-27

- 各設計シートの typo 修正・軽微な修正
  - 共通設計、ネットワーク設計、セキュリティ設計、バックアップ設計、ログ設計、監視設計、命名規則

## 2020-08-20

- パッチ管理設計シートの typo 修正
- 非機能要件ヒアリングシートの typo 修正（PR #3, #4）

## 2020-08-17

- 命名規則設計シートにタグ命名規則を追加

## 2020-08-03

- ログ設計シート（`cloud-design-log.md`）を追加
- ネットワークワークショップ資料（`workshop_network.md`）を追加

## 2020-07-27

- 非機能要件ヒアリングシート（`non-functional-requirement.md`）を新規作成
- `survey/` ディレクトリを新規作成
- 不要な LICENSE ファイルを整理

## 2020-06-26

- `design/README.md` のファイル一覧テーブルを更新

## 2020-06-18

- セキュリティ設計シートにセキュリティ調査強化サービスの図を追加

## 2020-05-25

- マルチアカウントワークショップ資料（`workshop_multiaccount.md`）を新規作成
- S3 ワークショップ資料の Macie 東京リージョン対応を修正

## 2020-05-13

- セキュリティ設計シートの typo 修正（PR #2）

## 2020-05-04 〜 2020-05-05

- 命名規則設計シート（`cloud-design-naming.md`）を新規作成
- S3 命名規則を追加
- ワークショップ資料（`workshop/`）を新規作成
  - S3 ワークショップ（`workshop_s3.md`）

## 2020-04-03

- セキュリティ設計シートのウイルス・マルウェア対策テーブルの表示を修正（PR #1）

## 2020-03-29（初回リリース）

- リポジトリ作成
- クラウド設計シート（`design/`）を新規作成
  - 可用性設計（`cloud-design-availability.md`）
  - AWS アカウント設計（`cloud-design-awsaccount.md`）
  - バックアップ設計（`cloud-design-backup.md`）
  - 共通設計（`cloud-design-common.md`）
  - 構成管理設計（`cloud-design-configurationmanagement.md`）
  - デプロイ・プロビジョニング設計（`cloud-design-deployprovisioning.md`）
  - 監視設計（`cloud-design-monitoring.md`）
  - ネットワーク設計（`cloud-design-network.md`）
  - パッチ管理設計（`cloud-design-patchmanagement.md`）
  - セキュリティ設計（`cloud-design-security.md`）
