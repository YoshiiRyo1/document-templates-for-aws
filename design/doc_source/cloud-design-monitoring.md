# 監視設計
監視を行う主な目的は以下の通り。  

* システムトラブルの予見・予防
* システムトラブルの発見
* システムトラブルの原因調査

## 要件

## モニタリング
パフォーマンスに関するメトリクス(レイテンシやリソース利用率等)に関するメトリクスを収集する。  
システムの正常性確認、異常の検知、インフラパフォーマンスベースライン取得を目的とする。  

収集は以下のツールを使用する。  

* CloudWatch
* CloudWatch Agent
* CloudWatch Logs
* RDS 拡張モニタリング
* RDS パフォーマンスインサイト
* Personal Health Dashboard

### CloudWatch
CloudWatch で AWS リソースをモニターする。  
AWS が定義しているメトリクスは自動的に収集される。  
CloudWatch が収集するメトリクスは各サービスのドキュメントを参照のこと。  

CloudWatch では以下のようにメトリクスデータが保持される。  

* 期間が 60 秒未満のデータポイントは、3 時間使用可能。
* 期間が 60 秒 (1 分) のデータポイントは、15 日間使用可能。
* 期間が 300 秒 (5 分) のデータポイントは、63 日間使用可能。
* 期間が 3600 秒 (1 時間) のデータポイントは、455 日 (15 か月) 間使用可能。

### CloudWatch Agent
EC2 のメモリ使用率やディスク使用率は CloudWatch 標準では収集できない。  
これらを収集するため EC2 には CloudWatch Agent をインストールする。  
CloudWatch Agent で収集されるメトリクスは AWS ドキュメントに記載がある。  
[CloudWatch エージェントにより収集されるメトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html)  

### CloudWatch Logs
OS やアプリケーションのログは CloudWatch Logs に集約する。  

AWS サービスでログ出力が可能なものは CloudWatch Logs へログ出力するように設定する。  
EC2 上のログは CloudWatch Agent でログを収集し CloudWatch Logs へ出力する。  

対象ログやログ保管期間はログ設計で定義する。  

### RDS 拡張モニタリング
DB インスタンスが実行されているオペレーティングシステム (OS) のリアルタイムのメトリクスを収集する。  

拡張モニタリングで収集されるメトリクスは AWS ドキュメントに記載がある。  
[拡張モニタリング](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)  

### RDS パフォーマンスインサイト
DB インスタンスの負荷をモニタリングし、データベースパフォーマンスの分析とトラブルシューティングを行うためにパフォーマンスインサイト使用する。  
アクティブセッション数や負荷のかかっている SQL 文などの通常 DBA が行うトラブルシューティングに必要な情報を収集されている。  

### Personal Health Dashboard
自社 AWS アカウントに影響するイベントが AWS で発生している場合に、アラートおよび改善のためのガイダンスを表示されるサービス。  
AWS リソースのリタイアメント (H/W 停止) やメンテナンス、リージョンレベルの障害などが表示される。   
AWS 内部障害を疑う場合には Personal Health Dashboard を確認する。  

## イベント監視
システムトラブルに関連するイベントを監視する。  
モニタリングしている情報からイベントを拾う。  

* AWS リソースのステータス変化
* サーバープロセスのダウン
* リクエスト数、トランザクション数の減少や増加
* 外形監視によるヘルスチェック
* 各種ログからキーワードフィルターによる検知
* AWS メンテナンス

### AWS リソースのステータス変化
CloudWatch で AWS リソースのステータス状態の変化を監視する。  
EC2インスタンスの場合、StatusCheckFailed等のステータスメトリクスでEC2インスタンスの正常性を監視。  

### サーバープロセスのダウン
CloudWatch Agent でサーバープロセス数をカウントしておき 0 になったらイベントにする。  

### リクエスト数、トランザクション数の減少や増加
リクエストやトランザクションが一定数発生している状態を正常とし、これらの極端や減少や増加をイベントとする。  
CloudWatch メトリクスから以下の値を取得する。  

|リソース|メトリクス例|
|---|---|
|S3|AllRequests,GetRequests,PutRequests|
|ALB|ActiveConnectionCount,RejectedConnectionCount,NewConnectionCount|
|RDS|DatabaseConnections,NetworkReceiveThroughput,NetworkTransmitThroughput,ReadIOPS,WriteIOPS|
|DynamoDB|ConsumedReadCapacityUnits,ConsumedWriteCapacityUnits|

### 外形監視によるヘルスチェック
サービスのエンドポイントに対して HTTP/HTTPS リクエストを行いステータスを確認する。  
サービスヘルス用のコンテンツを作成してこれに対してリクエストを行う。  
このコンテンツは WEB-AP-DB まで一気通貫でステータス確認ができるようにしておくことが望ましい。   

### 各種ログからキーワードフィルターによる検知
アプリケーションやミドルウェアが出力するログから特定文字列を検知しイベントとする。  
Error や Warning などの単語検知ではなく、具体的なイベント文字列を定義しておくことが望ましい。  

CloudWatch Logs にログを出力する際には、検索性を向上されるため JSON 形式での出力を検討する。  

### AWS メンテナンス



## 死活監視
ping や snmp 等での死活監視は行わない。  
CloudWatch メトリクスが定期的に取得されていることや前述したイベント監視で死活を行う。  

## 通知
