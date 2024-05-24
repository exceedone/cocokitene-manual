# (上級者向け、ベータ版)AWSによる環境構築-冗長構成
本ページでは、Amazon FargateでCocokiteneを構築する手順を記載します。  
Webサーバーのインストールをはじめとして、完全に新規にインストールを行う手順となります。  
また、本手順では**Webサーバーを冗長化した構成としております。**  
※シンプルな1台構成の環境構築方法は、[こちら](/ja/install_aws_single)をご確認ください。

## 環境
本ページでは、以下の構成で構築を行っております。
- VPC
- サブネット
    - パブリックA
    - パブリックB
- ルートテーブル
- インターネットゲートウェイ
- セキュリティグループ
- Amazon S3
- Amazon RDS
    - MySQL 8.0.x
- Amazon Elastic Load Balancing
    - Application Load Balancer
- System Manager - Parameter Store
- CloudWatch


## 注意点

- ご利用の環境やバージョンなどにより、本手順では正常に設定できない場合がございます。ご了承ください。

- 本手順では、CocokiteneをLinuxで動作させるための手順のみの記載となります。  

- **本手順では、管理者アカウントで実行している前提です。管理者アカウントでないユーザーが実行する場合、"sudo"を頭に付与してください。**

## 手順

### VPC
VPCを、以下の設定で作成します。

- 名前 : 任意 (例 : exment-vpc)
- IPv4 CIDR ブロック : XXX.XXX.0.0/16 (例 : 172.32.0.0/16)
- IPv6 CIDR ブロック : なし
- テナンシー : デフォルト
- DNS解決、DNSホスト名が無効になっている場合、それぞれ「有効」に変更


### サブネット
サブネットを、以下の設定で作成します。

#### パブリックサブネットA
- 名前 : 任意 (例 : cocokitene-subnet-public1)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1a
- IPv4 CIDR ブロック : XXX.XXX.1.0/24 (例 : 172.32.1.0/24)

#### パブリックサブネットB
- 名前 : 任意 (例 : cocokitene-subnet-public2)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1c
- IPv4 CIDR ブロック : XXX.XXX.2.0/24 (例 : 172.32.2.0/24)

#### パブリックサブネットA
- 名前 : 任意 (例 : cocokitene-subnet-public1)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1a
- IPv4 CIDR ブロック : XXX.XXX.1.0/24 (例 : 172.32.1.0/24)

#### パブリックサブネットB
- 名前 : 任意 (例 : cocokitene-subnet-public2)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1c
- IPv4 CIDR ブロック : XXX.XXX.2.0/24 (例 : 172.32.2.0/24)


### インターネットゲートウェイ
- 名前 : 任意 (例 : cocokitene-igw)
- 作成後、先ほど作成したVPCにアタッチ

### ルートテーブル
- 先ほど作成したVPCに、ルートテーブルが自動作成されています。  
Nameを編集することができます。(例 : cocokitene-rt)

- そのルートテーブルを選択し、ページ下部タブの「ルート」より、以下を追加します。
    - 送信先 : 0.0.0.0/0
    - ターゲット : Internet Gateway(上記で作成したインターネットゲートウェイ)

- ページ下部タブの「サブネットの関連付け」より、パブリックサブネットA、パブリックサブネットBを追加します。


### セキュリティグループ
セキュリティグループを、以下の設定で作成します。


#### セキュリティグループ-Web
- 名前 : 任意 (例 : cocokitene-sg-web)
- 説明 : 任意 (例 : cocokitene-sg-web)

■作成後、インバウンドのルール追加
- タイプ : HTTP
- ポート : 80
- ソース : 0.0.0.0/0, ::/0

#### セキュリティグループ-MySQL
- 名前 : 任意 (例 : cocokitene-sg-mysql)
- 説明 : 任意 (例 : cocokitene-sg-mysql)

■作成後、インバウンドのルール追加
- タイプ : MYSQL/Aurora
- ポート : 3306
- ソース : (「セキュリティグループ-Web」のグループID)


### AWS S3
- AWS S3のバケット作成、IAM作成を行います。  
上記URLの「S3用のIAMを作成」「Bucketの作成」を実施してください  
※バケットは、「添付ファイル用」「バックアップファイル用」「プラグイン用」「テンプレート用」の4種類作成してくだい


### MySQL
> このマニュアルでは、Amazon RDSを使用したMySQLの作成設定を記載しています。  

- エンジン : MySQL
- バージョン : 8.0.x
- インスタンス識別子 : 任意 (例 : cocokitene-db)
- DB インスタンスクラス : 任意 (例 : db.t2.micro)
- ユーザー名、パスワード : 各自に設定
- Virtual Private Cloud : 先ほど作成したVP
- 追加の接続設定
    - VPC セキュリティグループ : セキュリティグループ-MySQL
    - アベイラビリティーゾーン : 指定なし


### Fargate
このマニュアル[Fargate](/ja/install_aws_fargate)では、Amazon Fargateの作成設定を記載しています。 


### Amazon Elastic Load Balancing
- ロードバランサーの種類 : Application Load Balancer
- 名前 : 任意 (例 : cocokitene-alb)
- リスナー : HTTPもしくはHTTPS
- VPC : 作成したVPC
- アベイラビリティーゾーン : すべて選択
- セキュリティグループ : セキュリティグループ-Web
- ターゲットグループ : 任意 (例 : cocokitene-alb-tg)
- ターゲットグループインスタンス : 作成したWebサーバー2種類を選択肢、「登録済」に追加

設定完了後、ロードバランサーのDNS経由で、Webサーバーにアクセスできるようになります。


### その他

#### System Manager - Parameter Store
AWS Systems Manager Parameter Storeは、構成データ管理とシークレット管理のためのセキュアで階層的なストレージを提供します。パスワード、データベース文字列、ライセンスコードなどのデータをパラメータ値として保存できます。値は、プレーンテキストまたは暗号化データとして保存できます。その後、パラメータ作成時に指定した一意の名前を使用して値を参照できます。拡張性、可用性、耐久性に優れたParameter Storeは、AWSクラウドに支えられています。</br>
Parameter Storeを使用して、プロジェクトの環境変数を保存します。</br>
参照URL：[Systems Manager パラメータ作成](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-paramstore-su-create.html)

#### CloudWatch
Amazon CloudWatchは、Amazon Web Services（AWS）リソースとAWS上で実行するアプリケーションをリアルタイムで監視します。CloudWatchを使用して、リソースやアプリケーションを測定できる変数であるメトリクスを収集し、追跡することができます。</br>
[CloudWatch](https://d1.awsstatic.com/webinars/jp/pdf/services/20190326_AWS-BlackBelt_CloudWatch.pdf)