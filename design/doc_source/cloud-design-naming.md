# 命名規則
## 目的
命名規則を定める目的は以下の通り。  

* リソースの役割を示す
* 対象リソースの取り間違えを防ぐ
* 管理上の識別性

命名規則によって以下の識別を明確にする。  

* 対象システム
* 環境 (本番、開発、ステージングなど)
* AWS リソース (EC2、Security Group、RDS、ELB など)

## 基本ルール
命名規則の基本ルールは以下の通り。  

* 単語間はハイフン (-) で結ぶ
* 英小文字と数字のみを使用

マルチバイト文字、英大文字、記号、アンダースコア (_) は使用しないようにする。  

# 命名規則表
* 対象システムの名前(sysname)
    * システムで一意となる識別子(例：xxsystem)
* 環境(env)
    * 本番、検証、開発など(prod/stg/dev)
* ネットワークレイヤー(nlayer)
    * パブリック、プロテクト、プライベートなど(public/protected/private)
* 種別（type）
    * アプリケーションサーバー、踏み台サーバー、メールサーバーなど(app/bastion/mail)
* 目的（use）
    * ログ保管用、静的コンテンツ配信用など(log/contents)

## AWSリソース

|AWSリソース|命名規則|備考|
|---|---|---|
|VPC|{sysname}-{env}-vpc| |
|Subnet|{sysname}-{env}-{nlayer}-subnetXX|XXは連番、AZ毎に分ける|
|RouteTable|{sysname}-{env}-{nlayer}-rtb|NatGawatayをAZ毎に分ける場合はprotectedのみ連番を付与|
|InternetGateway|{sysname}-{env}-igw||
|ELB|{sysname}-{env}-alb/clb|インターナルなELBを作成する場合、役割毎に分ける場合はその部分も考慮|
|TargetGroup|{sysname}-{env}-tg|同上|
|EC2|{sysname}-{env}-{type}XX||
|IAMRole|{sysname}-{env}-{type}-role||
|SecurityGroup|{sysname}-{env}-{type}-sg||
|RDS|{sysname}-{env}-rds||
|S3|{sysname}-{env}-{use}-{AccountID}|下記「S3命名規則補足」を参照|

## S3命名規則補足
S3 バケット名称を決定する際は以下に留意する。  

* 全世界で一意になるようにする
* 3～63 文字以内にする
* 英文字で始まり、英数字で終わる
* 英数字とハイフン以外は使用しない、大文字またはアンダースコアを含めない
* 末尾にハイフンと数字を付けない
* 仮想ホスティング形式のバケットを使用するときは、バケット名にピリオド (「.」) を使用しない


# タグ
上記で定めた命名規則は Name タグに付与する。  

環境ごと、システムごとにリソースを検索することは、管理面やコスト把握に有用である。  
そのため Name タグの他に環境タグとシステムタグも付与する。  

## Technical Tags

|タグキー|タグ値|
|---|---|
|Name|命名規則で定めたリソース名称を入力|
|Environment|環境名 (prod/stg/dev など) |
|SystemName|システムを識別可能な文字列|
|ApplicationID|稼働しているアプリケーションを識別する文字列|
|ApplicationRole|アプリケーション機能 (Web/DB/batch など)|

## Automation
何らかのオペレーションを自動化している場合はタグを活用して対象システムやタスク実行時間を記述する。  

#### EC2 の日次起動停止を行っている場合の例

|タグキー|タグ値|
|---|---|
|EC2DailyStart|true、false など|
|EC2DailyStop|true、false など|
|EC2DailyStartTime|0800 など時刻を指定|
|EC2DailyStopTime|2100 など時刻を指定|

## ビジネスタグ
コストセンター、プロジェクト、顧客などの識別に活用する。  

|タグキー|タグ値|
|---|---|
|Project|プロジェクト名を識別する文字列|
|CostCenter|コストを負担する部門やチームを識別する文字列|
|Owner|当該リソースに関する問い合わせを誰にすればよいか、管理者や PM の氏名を入力|

