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

### 通知方法
イベントはメールで通知する。
イベント発行元 ～ EventBridge ～ SNS ～ メール という流れで通知を行う。

イベントは Notice と AR (Action Required)  に分類してメール送信する。
フィルタリングを容易にするため件名に分類を付与する。

| イベント | 件名に付与する接頭語 | 送信先メールアドレス |
| -------- | -------------------- | -------------------- |
| Notice   | Notice-              |                      |
| AR       | AR-                  |                      |

### AWS リソースのステータス変化
CloudWatch で AWS リソースのステータス状態の変化を監視する。  
EC2インスタンスの場合、StatusCheckFailed等のステータスメトリクスでEC2インスタンスの正常性を監視。  

| リソース | メトリクス                 | 説明                                                           | しきい値    | イベント分類 |
| -------- | -------------------------- | -------------------------------------------------------------- | ----------- | ------------ |
| EC2      | StatusCheckFailed_Instance | インスタンスのソフトウェア、ネットワーク起因によるダウンを検知 | 2分連続 2回 | AR           |
| EC2      | StatusCheckFailed_System   | AWS 内部のハードウェア障害によるダウンを検知                   | 2分連続 2回 | AR           |

### サーバープロセスのダウン
CloudWatch Agent でサーバープロセス数をカウントしておき 0 になったらイベントにする。  

| リソース   | メトリクス         | 説明                     | しきい値  | イベント分類 |
| ---------- | ------------------ | ------------------------ | --------- | ------------ |
| プロセス名 | カスタムメトリクス | サーバープロセスのダウン | 3分連続 0 | AR           |


### リクエスト数、トランザクション数の減少や増加
リクエストやトランザクションが一定数発生している状態を正常とし、これらの極端や減少や増加をイベントとする。  
CloudWatch メトリクスから以下の値を取得する。  

| リソース | メトリクス                | 説明                                                                    | しきい値      | イベント分類 |
| -------- | ------------------------- | ----------------------------------------------------------------------- | ------------- | ------------ |
| ALB      | ActiveConnectionCount     | クライアントからのアクティブ接続数                                      | 60分連続で 0  | AR           |
| ALB      | NewConnectionCount        | クライアントからの新規接続数                                            | 180分連続で 0 | AR           |
| ALB      | RejectedConnectionCount   | 接続可能な接続数を超えたリジェクト数                                    | 5分で 1 以上  | AR           |
| RDS      | DatabaseConnection        | DB コネクション数                                                       | 10分連続で 0  | AR           |
| RDS      | NetworkReceiveThroughput  | DB クラスターが各クライアントから受信したネットワークスループットの量   | 10分連続で 0  | AR           |
| RDS      | NetworkTransmitThroughput | DB クラスターが各クライアントに対し送信したネットワークスループットの量 | 10分連続で 0  | AR           |
| RDS      | ReadIOPS                  | 1 秒あたりのディスク I/O 操作の平均回数                                 | 10分連続で 0  | AR           |
| RDS      | WriteIOPS                 | 1 秒あたりのディスク I/O 操作の平均回数                                 | 10分連続で 0  | AR           |


### 外形監視によるヘルスチェック
サービスのエンドポイントに対して HTTP/HTTPS リクエストを行いステータスを確認する。  
サービスヘルス用のコンテンツを作成してこれに対してリクエストを行う。  
このコンテンツは WEB-AP-DB まで一気通貫でステータス確認ができるようにしておくことが望ましい。   

### 各種ログからキーワードフィルターによる検知
アプリケーションやミドルウェアが出力するログから特定文字列を検知しイベントとする。  
Error や Warning などの単語検知ではなく、具体的なイベント文字列を定義しておくことが望ましい。  

CloudWatch Logs にログを出力する際には、検索性を向上されるため JSON 形式での出力を検討する。  

| 対象ログ | 検出文字列                      | 説明                            | しきい値 | イベント分類 |
| -------- | ------------------------------- | ------------------------------- | -------- | ------------ |
| xxx      | { $.eventType = "UpdateTrail" } | xxxアプリケーションの実行エラー | 発生都度 | Notice       |

### AWS メンテナンス
Health イベントを取得して AWS メンテナンスを通知する。
対象は本プロジェクトで使用している全サービスの全イベントとする。

| 対象ログ   | イベントタイプ | 説明                          | しきい値 | イベント分類 |
| ---------- | -------------- | ----------------------------- | -------- | ------------ |
| EC2        | 全てのイベント | EC2 の Health イベント        | 発生都度 | Notice       |
| ELB        | 全てのイベント | ELB の Health イベント        | 発生都度 | Notice       |
| CloudFront | 全てのイベント | CloudFront の Health イベント | 発生都度 | Notice       |
| Route 53   | 全てのイベント | Route 53 の Health イベント   | 発生都度 | Notice       |


## 死活監視
ping や snmp 等での死活監視は行わない。  
CloudWatch メトリクスが定期的に取得されていることや前述したイベント監視で死活を行う。  

## 各アラートと対応手順


### セキュリティグループの作成、更新、削除

#### アラート名

CloudWatch Alarm | Security Group Configuration Changes

#### アラートの意味

セキュリティグループの構成変更を検知した。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

変更に心当たりが無い場合は、行われた変更を元に戻す作業を行う。  
IAM ユーザー/ロールの認証情報が漏洩したか、悪用された可能性がある。  
パスワード変更、及び、IAM ユーザーの一時停止を行い原因を解明する。  

セキュリティグループが不正に開放されてしまった場合は、元の状態に戻す。  
その接続元やポートからデータが持ち出されていないか等の調査を VPC Flow Logs から行う。  

#### アラート例

```
CloudWatch Alarm | Security Group Configuration Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [3.0 (21/02/21 23:25:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
SecurityGroupEventCount
```

### EC2 インスタンスの作成、終了、起動、停止、再起動

#### アラート名

CloudWatch Alarm | EC2 Instance Changes

#### アラートの意味

EC2 インスタンスの作成、終了、起動、停止、再起動が行われた。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

変更に心当たりが無い場合は、行われた変更を元に戻す作業を行う。  
IAM ユーザー/ロールの認証情報が漏洩したか、悪用された可能性がある。  
パスワード変更、及び、IAM ユーザーの一時停止を行い原因を解明する。  

不正アクセス被害のよくあるパターンに EC2 を大量に起動しコインマイニングを行う行為がある。  
そのような不正行為に EC2 が使われていないか調査する。  

#### アラート例

```
CloudWatch Alarm | EC2 Instance Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [2.0 (18/02/21 13:30:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
EC2InstanceEventCount
```

### IAM ポリシーの変更

#### アラート名

CloudWatch Alarm | IAM Policy Changes

#### アラートの意味

IAM ポリシーが変更された。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

変更に心当たりが無い場合は、行われた変更を元に戻す作業を行う。  
IAM ユーザー/ロールの認証情報が漏洩したか、悪用された可能性がある。  
パスワード変更、及び、IAM ユーザーの一時停止を行い原因を解明する。  

CloudTrail ログを検索し、変更を行った IAM ユーザー/ロールが前後で行った変更を洗い出し、影響範囲を明確にする。  
影響先も同じような調査を行い、バックドアが有無やデータ持ち出しの有無を明らかにする。  

#### アラート例

```
CloudWatch Alarm | IAM Policy Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [2.0 (19/02/21 05:38:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
IAMPolicyEventCount
```

### 許可されていない API の実行

#### アラート名

CloudWatch Alarm | Authorization Failures

#### アラートの意味

認められていない権限の操作が行われた。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

単なる操作間違いであれば対応は不要。  

IAM ユーザー/ロールが不正アクセスされてしまった際によく出るメッセージと言える。  
誰がどのような操作を行ったのかをしっかり押さえておくことが大切。  

#### アラート例

```
CloudWatch Alarm | Authorization Failures | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [42.0 (19/02/21 00:01:00)] was greater than or equal to the threshold (30.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
AuthorizationFailureCount
```

### AWS マネジメントコンソールへのログイン失敗

#### アラート名

CloudWatch Alarm | Console Sign-in Failures

#### アラートの意味

AWS マネジメントコンソールへのログイン失敗を検知した。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

単なるパスワード間違いであれば対応は不要。  

一度きりであれば大きな問題はない。  
何度もログイン試行されているようであれば、その IAM ユーザーは削除したほうがいい。  
また、IAM ユーザーの MFA 設定は徹底する。  

#### アラート例

```
CloudWatch Alarm | Console Sign-in Failures | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [4.0 (29/01/21 01:53:00)] was greater than or equal to the threshold (3.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
ConsoleSigninFailureCount
```

### VPC または VPC peering の作成、更新、削除

#### アラート名

CloudWatch Alarm | VPC Changes

#### アラートの意味

VPC または VPC peering の作成、更新、削除された。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

CloudTrail ログを検索し、どのようなリソースが作成されたのか影響を明らかにする。  

不正に作成された VPC や VPC peering からデータ漏えいが行われていないかを調査。  
VPC Flow Logs で不正に作成された Gateway 経由の通信を確認する。  

#### アラート例

```
CloudWatch Alarm | VPC Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [2.0 (01/02/21 06:08:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
VpcEventCount
```

### Internet Gateway または Customer Gateway の作成、更新、削除

#### アラート名

CloudWatch Alarm | Network Gateway Changes

#### アラートの意味

Internet Gateway または Customer Gateway の作成、更新、削除された。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

CloudTrail ログを検索し、どのようなリソースが作成されたのか影響を明らかにする。  

Gateway が作成されてそこからデータ漏えいが行われていないかを調査。  
VPC Flow Logs で不正に作成された Gateway 経由の通信を確認する。  

#### アラート例

```
CloudWatch Alarm | Network Gateway Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [2.0 (01/02/21 06:08:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
GatewayEventCount
```

### Network ACL の作成、更新、削除

#### アラート名

CloudWatch Alarm | NACL Changes 

#### アラートの意味

#### 受領後の対応

[CloudWatch Logs]() で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

Network ACL が不正に開放されてしまった場合は、元の状態に戻す。  
その接続元やポートからデータが持ち出されていないか等の調査を VPC Flow Logs から行う。  

#### アラート例

```
CloudWatch Alarm | NACL Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [5.0 (02/02/21 06:29:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
NetworkACLEventCount
```

### CloudTrail の作成、更新、削除、ロギングのスタート、ロギングのストップ

#### アラート名

CloudWatch Alarm | CloudTrail Changes

#### アラートの意味

CloudTrail の作成、更新、削除、ロギングのスタート、ロギングのストップが行われた。  

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

行われた変更を元に戻す作業を行う。  

IAM ユーザー/ロールの認証情報が漏洩したか、悪用された可能性がある。  
パスワード変更、及び、IAM ユーザーの一時停止を行い原因を解明する。  

#### アラート例

```
CloudWatch Alarm | CloudTrail Changes | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [1.0 (15/02/21 00:46:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
CloudTrailEventCount
```

### S3 バケットポリシーの変更

#### アラート名

CloudWatch Alarm | S3 Bucket Activity 

#### アラートの意味

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

変更に心当たりが無い場合は、行われた変更を元に戻す作業を行う。  

S3 アクセスログを検索しデータ漏えい有無を確認する。  

#### アラート例

```
CloudWatch Alarm | S3 Bucket Activity | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 1 out of the last 1 datapoints [1.0 (19/02/21 09:41:00)] was greater than or equal to the threshold (1.0) (minimum 1 datapoint for OK -> ALARM transition).
Alarm State
ALARM
Namespace
CloudTrailMetrics
Metric
S3BucketActivityEventCount
```

### 管理者権限の使用

#### アラート名

CloudWatch Alarm | Root User Use

#### アラートの意味

AWS アカウント Root ユーザーが使用された

#### 受領後の対応

[CloudWatch Logs](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#logsV2:log-groups) で該当をイベントを特定する。  

- 誰が (userIdentity.arn)
- いつ (eventTime)
- どのリソースに対してどのような変更を行ったのか (requestParameters)

変更に心当たりがある正当なものであれば対応は不要。  

root ユーザーはクラスメソッド社が管理している。  
通常は使用されることがない。  
使用された際はクラスメソッドに問い合わせを行う。  

#### アラート例

```

```

### ヘルスイベント

#### アラート名

AWS Health Event

#### アラートの意味

AWS でメンテナンスや障害のイベントが発生した。  

#### 受領後の対応

イベント内容を確認する。  

エラー率の上昇の場合、直接的な影響が及んでいなくとも、他のアラートやメトリクスから本サービスに影響が出ていないかは注視が必要。  
connectivity issues の場合、本システムに影響が及んでいる可能性が高い。  
Multi-AZ になっていれば ALB/NLB で障害が起きている AZ を切り離す。  
[複数のAvailability ZoneにプロビジョニングしたELB(ALB) / AutoScaling Groupから特定Availability Zone上のリソースをパージする](https://dev.classmethod.jp/articles/purge-resources-specific-az/)  

スケジュールされたイベントの場合、古いインスタンスのリタイアメントなど定期的なメンテナンスを示している。  
通知されている内容に従う。  

#### アラート例

```
AWS Health Event | ap-northeast-1 | Account: nnnnnnnnnnnn | open
Event type code: AWS_ELASTICLOADBALANCING_API_ISSUE
現在、ap-northeast-1 リージョンでの、ELB API エラー率の上昇について調査を進めております。既存のロードバランサーへの接続には影響はありません。 | We are investigating increased error rates for ELB APIs in the ap-northeast-1 Region. Connectivity to existing load balancers is not affected.
```

### GuardDuty

#### アラート名

GuardDuty Finding

#### アラートの意味

GuardDuty が通常ではない振る舞いを検知した。  

#### 受領後の対応

[GuardDuty](https://ap-northeast-1.console.aws.amazon.com/guardduty/home?region=ap-northeast-1#/findings?macros=current) から該当イベントを確認する。  

影響範囲は GuardDuty 該当イベント内にリンクされている Detective から調査可能。  

修復方法は [公式ドキュメント](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-active.html) を参考に行い、社内にナレッジを蓄積していく。  

#### アラート例

```
GuardDuty Finding  | ap-northeast-1 | Account: nnnnnnnnnnnn
Finding type: Behavior:EC2/NetworkPortUnusual
EC2 instance i-nnnnnnnnnnnn is communicating with a remote host on an unusual server port 587.
First Seen
Sun, 21 Feb 2021 01:39:55 GMT
Last Seen
Sun, 21 Feb 2021 01:40:55 GMT
Severity
MEDIUM
Threat Count
1
Affected Resource
i-nnnnnnnnnnnn
```

### EC2 Auto Recovery

#### アラート名

CloudWatch Alarm | recover-ec2-anywhere-prod-{EC2名}

#### アラートの意味

AWS 側で何らかの障害が発生し、EC2 が自動的に再起動を行い復旧した。  

#### 受領後の対応

EC2 が正常に動作していれば対応は不要。  
その他アラートやメトリクスで異常が無いことを確認する。  

#### アラート例

```
CloudWatch Alarm | recover-ec2-anywhere-dev-batch | ap-northeast-1 | Account: nnnnnnnnnnnn
Threshold Crossed: 2 datapoints [1.0 (29/01/21 05:16:00), 1.0 (29/01/21 05:15:00)] were greater than the threshold (0.0).
Alarm State
ALARM
Namespace
AWS/EC2
Metric
StatusCheckFailed_System
InstanceId
i-nnnnnnnnnnnn
```

### EC2 CPU使用率上昇

#### アラート名

CloudWatch Alarm | EC2_CPUUtilization-{インスタンスID}

#### アラートの意味

EC2 の CPU 使用率が上昇したままの状態が続いている。  

#### 受領後の対応

[CloudWatch メトリクス](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#metricsV2:graph=~()) で事象や発生時間帯を確認する。  

OS へログインし、ps, top コマンド等で CPU リソースを消費しているプロセスを特定する。  

そのプロセス (アプリケーション) のログやダンプから原因を調査する。  

CPU 使用率を下げるために必要に応じて再起動を行う。  

CPU 使用率の上昇が正常な挙動である場合は、EC2 のスペックアップを検討する。  

#### アラート例

```
CloudWatch Alarm | EC2_CPUUtilization-i-nnnnnnnnnnnn | ap-northeast-1 | Account: nnnnnnnnnnnn

```

### EC2 空きディスク容量枯渇

#### アラート名

CloudWatch Alarm | EC2_Disk_Used_Percent-{インスタンスID}

#### アラートの意味

EC2 のディスク空き容量が枯渇している状態が続いている。  

#### 受領後の対応

[CloudWatch メトリクス](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#metricsV2:graph=~()) で事象や発生時間帯を確認する。  

OS へログインし、du コマンド等で使用容量の多いディレクトリを特定する。  

不要なファイルであれば削除する。  
また、ログローテション等の定期削除を仕込む。  

必要なファイルで削除できない場合は、EBS 拡張を検討する。  
[EBS ボリュームへの変更のリクエスト](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/requesting-ebs-volume-modifications.html)  
[ボリュームサイズ変更後の Linux ファイルシステムの拡張](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)  

#### アラート例

```
CloudWatch Alarm | EC2_Disk_Used_Percent-i-nnnnnnnnnnnn | ap-northeast-1 | Account: nnnnnnnnnnnn
```

### EC2 空きメモリ枯渇

#### アラート名

CloudWatch Alarm | EC2_Mem_Used_Percent-{インスタンスID}  

#### アラートの意味

EC2 の空きメモリが枯渇している状態が続いている。  

#### 受領後の対応

[CloudWatch メトリクス](https://ap-northeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-1#metricsV2:graph=~()) で事象や発生時間帯を確認する。  

OS へログインし、ps, top コマンド等でメモリリソースを消費しているプロセスを特定する。  

#### アラート例

```
CloudWatch Alarm | EC2_Mem_Used_Percent-i-nnnnnnnnnnnn | ap-northeast-1 | Account: nnnnnnnnnnnn
```

