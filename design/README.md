# クラウドデザインシート
クラウド上にシステム構築を行う前の設計フェーズで使用するドキュメントを想定して作成しました。  
一般的に言う「インフラ基本設計」にあたる文書です。  

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

| ファイル名                                                                                    | 内容の説明                                       |
| --------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| [cloud-design-common.md](doc_source/cloud-design-common.md)                                   | アーキテクチャ共通の項目について記載             |
| [cloud-design-availability.md](doc_source/cloud-design-availability.md)                       | システムの可用性について記載                     |
| [cloud-design-configurationmanagement.md](doc_source/cloud-design-configurationmanagement.md) | 構成管理について記載                             |
| [cloud-design-deployprovisioning.md](doc_source/cloud-design-deployprovisioning.md)           | デプロイ・プロビジョニングの考え方について記載   |
| [cloud-design-monitoring.md](doc_source/cloud-design-monitoring.md)                           | 監視設計について記載                             |
| [cloud-design-network.md](doc_source/cloud-design-network.md)                                 | AWS ネットワーク設計について記載                 |
| [cloud-design-patchmanagement.md](doc_source/cloud-design-patchmanagement.md)                 | EC2 やマネージドサービスのパッチ管理について記載 |
| [cloud-design-security.md](doc_source/cloud-design-security.md)                               | AWS セキュリティついて記載                       |
| [cloud-design-awsaccount.md](doc_source/cloud-design-awsaccount.md)                           | AWS アカウント構成について記載                   |
| [cloud-design-backup.md](doc_source/cloud-design-backup.md)                                   | バックアップについて記載                         |
| [cloud-design-naming.md](doc_source/cloud-design-naming.md)                                   | リソース命名について記載                         |
| [cloud-design-log.md](doc_source/cloud-design-log.md)                                         | ログ管理について記載                             |


## MS Word で使いたい場合
MS Word で使いたい場合はお手数ですが、VSCode-pandoc などを使い Word ファイルへ変換をお願いいたします。  

