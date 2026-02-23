# ヒアリングシート
クラウド上にシステム構築を行う前の要件定義、設計フェーズで使用するヒアリングシートを作成しました。

## 対象利用者
以下のような利用者を想定しています。

* AWS 上で自社サービスを構築するインフラ担当者
* 社内システムを AWS へ移行する計画を持つ情報システム部門のエンジニア
* お客様にシステム構築を提案する立場のベンダー担当者


## 使い方
doc_source にあるファイルをローカル PC へコピーし設計書のテンプレートとしてお使いください。

一般的な項目は記載したつもりですが、システムごとに不足している項目は追記してお使いください。
また、記載すみの項目において不要な項目は削除してお使いください。

汎用的に使えるように意識して書きました。
要件ごとに考え方が変わるような項目はボンヤリとした書き方になっているかもしれません。
このあたりは使いやすさを考慮してアップデートしていく予定です。

AWS Well-Architected フレームワークの内容に合致するよう記載したつもりです。
今後もアップデートは随時行なっていく予定です。

## ファイル一覧

| ファイル名                                                                  | 内容の説明                                                                 | 質問数 |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ------ |
| [nfr-reliability.md](./doc_source/nfr-reliability.md)                       | 信頼性（稼働率、バックアップ、災害対策）                                   | 27     |
| [nfr-security.md](./doc_source/nfr-security.md)                             | セキュリティ（コンプライアンス、データ保護、ID管理、攻撃防御、パッチ管理） | 47     |
| [nfr-operational-excellence.md](./doc_source/nfr-operational-excellence.md) | 運用上の優秀性（運用体制、ジョブ、ログ、監視、デプロイ）                   | 46     |
| [nfr-performance-efficiency.md](./doc_source/nfr-performance-efficiency.md) | パフォーマンス効率（性能目標、拡張性、キャッシュ、テスト）                 | 24     |
| [nfr-cost-optimization.md](./doc_source/nfr-cost-optimization.md)           | コスト最適化                                                               | 15     |
| [nfr-sustainability.md](./doc_source/nfr-sustainability.md)                 | サステナビリティ                                                           | 7      |
| [nfr-infrastructure.md](./doc_source/nfr-infrastructure.md)                 | 環境（AWSアカウント、ネットワーク、ドメイン、ライセンス）                  | 19     |
| [nfr-migration.md](./doc_source/nfr-migration.md)                           | マイグレーション                                                           | 14     |
| [nfr-generative-ai.md](./doc_source/nfr-generative-ai.md)                   | 生成 AI（ガバナンス、セキュリティ、運用、パフォーマンス、コスト）          | 7      |

## 謝辞

当資料を作るにあたり、情報処理推進機構様の「非機能要求グレード」を参照情報として活用させていただきました。  
https://www.ipa.go.jp/archive/digital/iot-en-ci/jyouryuu/hikinou/ent03-b.html
