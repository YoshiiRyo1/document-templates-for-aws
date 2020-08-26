# ログ
AWS 上でログを取得・保管する方法を記述する。  


## 要件

## ログ保管期間


## S3アクセスログ
不正アクセス調査、S3 へアクセスするプログラムの障害調査のため S3 アクセスログを保管する。  
S3 のログ記録は以下の2通りある。  

* サーバーアクセスのログ記録
* オブジェクトレベルのログ記録(CloudTrail)

### サーバーアクセスのログ記録
バケットに対するリクエストの詳細が記録される。  
セキュリティ監査やアクセス解析に使用する。  

サーバーアクセスログ取得の有効化はバケット(ソースバケット)ごとに指定する。  
有効にした際にキープレフィックスを指定可能。キープレフィックスは分類を容易にするためソースバケット名とする。  

サーバーアクセスログ保管用のバケット(ターゲットバケット)を作成し、サーバーアクセスログはここに集約する。  
ソースバケットとターゲットバケットの両方を同じ AWS アカウントが所有すること、さらに両方のバケットが同じリージョンにあることが必要。  

アクセスログのフォーマットは公式ドキュメントに記述がある。  
[Amazon S3 サーバーアクセスログの形式](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/LogFormat.html)  

### サーバーアクセスログのクエリ
Athena を使用してサーバーアクセスログをクエリする。  
クエリ方法、サンプルは公式ドキュメントに記述がある。  
[Amazon Athena を使用した Amazon S3 アクセスログでのリクエストのクエリ](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/using-s3-access-logs-to-identify-requests.html#querying-s3-access-logs-for-requests)  

### オブジェクトレベルのログ記録(CloudTrail)
CloudTrail データイベント機能(追加料金)を使用してオブジェクトレベルのAPIアクティビティを記録する。  
オブジェクトレベルで誰が何をしたかを監査する場合はこちらを併用する。  

CloudTrail データイベント機能はバケットごとに有効にする。  

### オブジェクトレベルのログクエリ
Athena を使用して CloudTrail のログをクエリする。  
クエリ方法、サンプルは公式ドキュメントに記述がある。  
[AWS CloudTrail ログのクエリ](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/cloudtrail-logs.html)

## ALB アクセスログ
トラブルシューティング、フォレンジック調査のために ALB アクセスログを保管する。  
ALB アクセスログ専用の S3 バケットを作成し、ここに全 ALB のアクセスログを集約する。  
ALB アクセスログ専用 S3 バケットのキープレフィックスは ALB 名とする。  

ALB アクセスログのフォーマットは公式ドキュメントに記述がある。  
[アクセスログのエントリ](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/load-balancer-access-logs.html#access-log-entry-format)

### ALB アクセスログのクエリ
Athena を使用して ALB アクセスログをクエリする。  
クエリ方法、サンプルは公式ドキュメントに記述がある。  
[Application Load Balancer ログのクエリ](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/application-load-balancer-logs.html)

## CloudFront アクセスログ
トラブルシューティング、フォレンジック調査のために ALB アクセスログを保管する。  
CloudFront アクセスログ専用の S3 バケットを作成し、ここに全 CloudFront のアクセスログを集約する。  
CloudFront アクセスログ専用 S3 バケットのキープレフィックスは CloudFront CNAME の FQDN とする。  

### CloudFront アクセスログのクエリ
Athena を使用して CloudFront アクセスログをクエリする。  
クエリ方法、サンプルは公式ドキュメントに記述がある。  
[Amazon CloudFront ログのクエリ](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/cloudfront-logs.html)

## AWS WAF トラフィックログ
WebACL に関する詳細情報を取得する。  
トラブルシューティング、フォレンジック調査に加えて、設定している各ルールのカスタマイズ(有効無効)の判断に利用する。  
ログ取得のために Kinesis Data Firehose と S3 バケットをバージニア北部リージョンで作成する。  

### AWS WAF トラフィックログのクエリ
Athena を使用して AWS WAF トラフィックログをクエリする。  
クエリ方法、サンプルは公式ドキュメントに記述がある。  
[AWS WAF ログのクエリ](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/waf-logs.html)


## EC2 
CloudWatch Agent を使用して OS 上のログファイルを CloudWatch Logs へ集約する。  
集約対象ログは以下の通り。

|サーバー名|ログ種別|ファイルパス|
|---|---|---|
|APサーバー|セキュリティログ|/var/log/secure|
|APサーバー|システムログ|/var/log/messages|

#### CloudWatch Logs へ集約することの意味

* 長期保管が可能になる
* 複数のインスタンスのログをグループ化することで検索/分析が効果的に行える

