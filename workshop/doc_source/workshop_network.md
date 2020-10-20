## ネットワーク基礎

### VPC
Virtual Private Cloud の略です。  
その名の通り AWS 上に仮想的なプライベートネットワーク空間を構築します。  

VPC は AWS アカウント内に独立したネットワーク空間を提供します。  
VPC の中にサブネットを作り、サブネットで AWS サービス (EC2 や RDS など) を動作させます。  

AWS Blackbelt 「Amazon Virtual Private Cloud (VPC) Basic」の P.16～22 を参照して VPC 基本を確認してみます。  
<iframe src="//www.slideshare.net/slideshow/embed_code/key/83EjIHmvt0b8QH?startSlide=16" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/AmazonWebServicesJapan/20190313-aws-black-belt-online-seminar-amazon-vpc-basic" title="20190313 AWS Black Belt Online Seminar Amazon VPC Basic" target="_blank">20190313 AWS Black Belt Online Seminar Amazon VPC Basic</a> </strong> from <strong><a href="//www.slideshare.net/AmazonWebServicesJapan" target="_blank">Amazon Web Services Japan</a></strong> </div>


VPC を作成するときに、その VPC の IPv4 CIDR ブロックを指定する必要があります。許可されるブロックサイズは、/16 ネットマスク（65,536 個の IP アドレス）から /28 ネットマスク（16 個の IP アドレス）の間です。  

VPC を作成する場合は、 RFC 1918 に指定されているように、プライベート IPv4 アドレス範囲から CIDR ブロック (/16 以下) を指定することをお勧めします。  

* 10.0.0.0 - 10.255.255.255 (10/8 プレフィックス)
* 172.16.0.0 - 172.31.255.255 (172.16/12 プレフィックス)
* 192.168.0.0 - 192.168.255.255 (192.168/16 プレフィックス)

### リージョン
データセンターが集積されている物理的ローケーションをリージョンと呼びます。  
リージョンは世界中に存在していて、日本では東京リージョンが利用可能です。(2020年7月30日時点)  
※大阪ローカルリージョンも存在しますが、東京リージョンの DR 先といった位置付けで全ての機能が使えるわけではありません。(2020年7月30日時点)  

[利用できるリージョン](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions)

### Availability Zone
1つ以上のデータセンターで Availability Zone(AZ) を構成しています。  
AZ はお互いに地理的・電源的・ネットワーク的に分離されています。  
冗長構成をとる場合は AZ をまたぐように構成することがベストプラクティスです。  

1つのリージョンは複数の AZ で構成されています。  

同じリージョン内の AZ 間は高速専用線で接続されているので、ほとんどアプリケーションで AZ をまたぐことは問題になりません。  
レイテンシにシビアなアプリケーションにおいては同じ AZ にサーバー郡を配置することを検討します。  

### サブネット
サブネットは AZ ごとに作成します。AZ をまたいだサブネットを作成することはできません。    

VPC CIDR ブロックの範囲でサブネットを作成してします。  
サブネットのブロックサイズは /28 ネットマスクから /16 ネットマスクの間です。  

サブネットの CIDR ブロックの最初の 4 つの IP アドレスと最後の IP アドレスは使用できません。  
サブネットを細かく分割しぎると IP アドレスの無駄になりかねませんのでご注意ください。  

#### CIDR ブロック 10.0.0.0/24 を持つサブネットの例

|IP アドレス|説明|
|---|---|
|10.0.0.0|ネットワークアドレスです。|
|10.0.0.1|VPC ルーター用に AWS で予約されています。|
|10.0.0.2|AWS が提供する DNS サーバーで予約されています。|
|10.0.0.3|将来の利用のために AWS で予約されています。|
|10.0.0.255|ネットワークブロードキャストアドレスです。VPC ではブロードキャストがサポートされないため、このアドレスを予約します。|


#### サブネット作成の考え方
AWS 上のサブネットを作成する基準は後述するルートテーブルと Network ACL です。  
例えば、インターネットへの経路を持つサブネット、持たないサブネットの2種類という形で作成します。  

オンプレミスのようにトラフィックを分ける目的で業務セグメント、運用セグメントを用意する必要はありません。  
大きなサブネットを用意してフラットに運用していくことが、拡張性や運用の容易さを考えるうえで重要です。  

### セキュリティグループ
仮想ファイアウォールとして機能するのがセキュリティグループです。  
セキュリティグループでは受信トラフィック、送信トラフィックのそれぞれを制御可能です。  

送信元または送信先、プロトコル、ポートの範囲を組み合わせてルールを作成します。  
ルールで明示的に許可した通信のみが行われ、ルールに無い通信は拒否されます。(明示的な拒否ルールは作れません)  

セキュリティグループはステートフルです。  
許可した受信リクエストに対応する応答を送信許可する必要はありません。  

通信要件に合わせたサーバーグループごとにセキュリティグループを作成します。  
例えば、同じ役割をもった WEB サーバーが10台あった場合には 1つのセキュリティグループを10台の WEB サーバーに割り当てます。  

### Network ACL
1つ以上のサブネットの受信トラフィック、送信トラフィックを制御するファイアウォールとして機能します。  
Network ACL はサブネット、セキュリティグループはインスタンスと考えて頂けると理解が容易だと思います。  

ルール番号、送信元または送信先、プロトコル、ポートの範囲、拒否または許可を組み合わせてルールを作成します。  
ルール番号の低い順から評価をします。  

Network ACL はステートレスです。  
受信、送信とも明示的に許可または拒否をする必要があります。  

管理上の複雑さを減らすために、ネットワーク通信制御はまずはセキュリティグループで行い、  
セキュリティグループで対応しきれない要件を Network ACL で対応することをおすすめします。  

### Internet Gateway
VPC とインターネット間の相互通信を可能にするコンポーネントです。  
外部との送受信が発生するリソース (Load Balancer や Web サーバーなど) が存在する VPC には Internet Gateway を設置します。  

可能性や帯域幅のスケールは AWS が管理しています。  

### NAT Gateway
プライベートサブネットの AWS リソースからインタネットへの接続が必要な場合は NAT Gateway を使用します。  
NAT Gateway では、プライベートサブネットの AWS リソースからインターネットへの送信を行い、その応答を AWS リソースへ返送します。  
トラフィックがインターネットに送信される際、送信元の IPv4 アドレスは NAT Gateway の IP アドレスに置き換えられます。  

複数サーバーで他システム連携がある場合、送信元グローバル IP アドレスを1つにしたい場合に使用できます。  

### ルートテーブル
ネットワーク経路を決めるためのルート情報を決めるための一連のルールが含まれているテーブルです。  
ルートテーブルにルート情報を定義し、ルートテーブルをサブネットに関連付けて使用します。  

#### ルート
送信先とターゲットの2つを指定してルート情報を定義します。  

例) デフォルトルートを Internet Gateway に向ける

|送信先|ターゲット|
|---|---|
|0.0.0.0/32|igw-12345678901234567|

#### ルーティングの優先度
ルートテーブルは Longest Match です。  
送信先に指定した CIDR ブロックのうち最も具体的なルートが選択されます。  

#### ローカルルート
VPC の CIDR ブロックはローカルルートとして全てのルートテーブルに含まれます。  
サブネットに関連付けられているルートテーブルでは、ローカルルートは最も優先されます。  

例) VPC CIDR ブロックが 10.0.0.0/16 だった場合

|送信先|ターゲット|
|---|---|
|10.0.0.0/16|local|


### Elastic IP
静的なグローバル IP アドレス (IPv4) です。  
インスタンスではなくネットワークインターフェイスに関連付けます。  

インスタンス単位で固定グローバル IP アドレスが必要な場合に Elastic IP を取得します。  
未割り当ての Elastic IP は1時間毎に利用料金が発生します。Elastic IP の取得は確実に使用する分のみに抑えます。  

### VPC での DNS
デフォルトでは VPC での名前解決は AWS が用意している DNS サーバーを使用します。  
独自の DNS サーバーや Route 53 を指定することも可能です。  

### 時刻同期
AWS は Amazon Time Sync Service という時刻同期サービスを提供しています。  
特殊な要件がない限り、Amazon Time Sync Service で時刻同期を行います。  



## ハイブリットネットワーク
拠点・データセンター ～ AWS 間、AWS ～ AWS 間、他クラウド ～ AWS 間を接続するハイブリットネットワークについて記述します。  
ハイブリットネットワークを採用する場合、接続元と VPC の CIDR ブロックは重複できません。その前提で設計をお願いします。  

### Direct Connect
拠点やデータセンターから AWS の間を高品質・低レイテンシの専用線で接続するサービスです。  
![Direct Connect 連結図](https://docs.aws.amazon.com/ja_jp/directconnect/latest/UserGuide/images/direct_connect_overview.png)  
*https://docs.aws.amazon.com/ja_jp/directconnect/latest/UserGuide/Welcome.html より引用*

#### 接続
拠点ネットワークと AWS Direct Connect の1つの場所に専用のネットワーク接続を確立します。  
接続には2種類の方法があります。  

* 専用接続  
* ホスト接続

##### 専用接続  
自社専用の物理接続です。1Gbpsまたは10Gbps帯域を専有することが可能になります。  
Direct Connect 接続サービスを提供しているネットワークキャリアと契約します。  

##### ホスト接続
Direct Connect パートナーから仮想的な接続が提供されます。  
パートナーのサービスメニューによって1Gbps未満の契約が可能です。  

#### 仮想インターフェイス
Direct Connect 接続を開始するために仮想インターフェイス (VIF) を作成します。  

##### プライベート仮想インターフェイス
プライベート IP アドレスを使って Amazon VPC へアクセスするために使用します。  
もっとも多いパターンです。  

##### パブリック仮想インターフェイス
パブリック IP アドレスを使用してすべての AWS パブリックサービスにアクセスします。  
S3 や Connect などの Private Link を持たないサービスに閉域で接続したい場合を想定しています。  

##### トランジット仮想インターフェイス
後述する Direct Connect Gateway と Trangit Gateway の組み合わせで利用します。  

### Direct Connect Gateway
グローバルな Direct Connect 用のゲートウェイです。  
あるリージョンで Direct Connect を開設し、それを Direct Connect Gateway に関連付けると拠点から他リージョンへアクセスすることが可能になります。  

![Direct Connet Gateway](https://docs.aws.amazon.com/ja_jp/directconnect/latest/UserGuide/images/dx-gateway.png)  
*https://docs.aws.amazon.com/ja_jp/directconnect/latest/UserGuide/direct-connect-gateways-intro.html より引用*

Direct Connect Gateway を経由した VPC 間通信ができないのでご注意ください。  
上の例だと、US West ～ US East 間の VPC 通信にあたります。(VPC Peering や Trangit Gateway を検討します)  

### Site-to-Site VPN
オンプレミスまたは他クラウドと VPC を VPN で接続するサービスです。  
AWS が接続される側になります。オンプレミスまたは他クラウドのルーター機器はお客様でご用意いただく必要があります。    

Virtual Private Gateway に接続するパターンと Trangit Gateway に接続するパターンがあります。  
複数の VPC を接続した場合は Transit Gateway を選択します。  

![Site-to-Site VPN](https://docs.aws.amazon.com/ja_jp/vpn/latest/s2svpn/images/Branch_Offices_diagram.png)  

![Site-to-Site VPN](https://docs.aws.amazon.com/ja_jp/vpn/latest/s2svpn/images/branch-off-transit-gateway.png)  
*https://docs.aws.amazon.com/ja_jp/vpn/latest/s2svpn/Examples.html より引用*


### Trangit Gateway
オンプレミス ～ VPC、VPC ～ VPC 間接続の中央ハブ、クラウドルーターとして機能するサービスです。  

Transit Gateway 登場以前は、オンプレミス拠点数 ✕ VPC 数  の接続をメッシュ型で行っていました。  
Transit Gateway によって集中管理を行うことでハイブリットネットワーク管理上の手間削減が可能です。  

**AWS Transit Gateway を使用しない場合**  
![AWS Transit Gateway を使用しない場合](https://d1.awsstatic.com/product-marketing/transit-gateway/tgw-before.7f287b3bf00bbc4fbdeadef3c8d5910374aec963.png)  

**AWS Transit Gateway を使用した場合**  
![AWS Transit Gateway を使用した場合](https://d1.awsstatic.com/product-marketing/transit-gateway/tgw-after.d85d3e2cb67fd2ed1a3be645d443e9f5910409fd.png)  

#### ルートの制御
Transit Gateway でルートテーブルを制御できます。  
例えば、ある拠点からは特定の VPC しかアクセスさせない、VPC-A と VPC-B は通信可能だが VPC-C は通信不可、といったことが可能です。  

### 参考
[顧客拠点から Amazon VPCへの接続パターンまとめ (Whitepaper参照）](https://dev.classmethod.jp/articles/whitepaper-translate-jpn-vpc-connectivity-options-01/)  
[Amazon VPC同士の接続パターンまとめ (Whitepaper参照）](https://dev.classmethod.jp/articles/whitepaper-vpc-conectivity-options-02/)


