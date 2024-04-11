# (上級者向け、ベータ版)Azure Web Appsによる環境構築
本ページでは、Azure Web AppsでExmentを構築する手順を記載します。  
Azure Web AppsならびにMySQLを作成し、実行するための手順です。

## 注意点

- ご利用の環境やバージョンなどにより、本手順では正常に設定できない場合がございます。ご了承ください。

- 本手順では、ExmentをAzure Web Appsで動作させるための手順のみの記載となります。  
Azure Web Appsの一般的なIT系のナレッジについては記載致しておりません。ご了承ください。  

## 手順

### MySQL作成
- Azureポータルで、MySQLを作成します。



### Azure Web Apps
- Azureポータルで、Azure Web Appsを作成します。  
その後、Azureのリソースにアクセスします。
(要画像)


- 左メニューより、「拡張機能」→「Composer」を選択し、composerをインストールします。

- 左メニューより、「高度なツール」を選択し、「移動」を選択します。

- KuduのGUIで、以下のパスに移動します。
D:\home\site

- 以下のコマンドを実行します。

```
composer create-project laravel/laravel  --prefer-dist . "6.*" 
composer require exceedone/exment
php artisan vendor:publish --provider="Exceedone\Exment\ExmentServiceProvider"
```



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
- 名前 : 任意 (例 : exment-subnet-public1)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1a
- IPv4 CIDR ブロック : XXX.XXX.1.0/24 (例 : 172.32.1.0/24)

#### パブリックサブネットB
- 名前 : 任意 (例 : exment-subnet-public2)
- VPC : 上記で作成したVPC
- アベイラビリティーゾーン : ap-northeast-1c
- IPv4 CIDR ブロック : XXX.XXX.2.0/24 (例 : 172.32.2.0/24)


### インターネットゲートウェイ
- 名前 : 任意 (例 : exment-igw)
- 作成後、先ほど作成したVPCにアタッチ

### ルートテーブル
- 先ほど作成したVPCに、ルートテーブルが自動作成されています。  
Nameを編集することができます。(例 : exment-rt)

- そのルートテーブルを選択し、ページ下部タブの「ルート」より、以下を追加します。
    - 送信先 : 0.0.0.0/0
    - ターゲット : Internet Gateway(上記で作成したインターネットゲートウェイ)

- ページ下部タブの「サブネットの関連付け」より、パブリックサブネットA、パブリックサブネットBを追加します。


### セキュリティグループ
セキュリティグループを、以下の設定で作成します。

#### セキュリティグループ-SSH
- 名前 : 任意 (例 : exment-sg-ssh)
- 説明 : 任意 (例 : exment-sg-ssh)
- VPC : 上記で作成したVPC

■作成後、インバウンドのルール追加
- タイプ : SSH
- ポート : 22
- ソース : SSH接続を行うIPアドレス
- VPC : 上記で作成したVPC

#### セキュリティグループ-SSHプライベート
- 名前 : 任意 (例 : exment-sg-ssh-private)
- 説明 : 任意 (例 : exment-sg-ssh-private)
- VPC : 上記で作成したVPC

■作成後、インバウンドのルール追加
- タイプ : SSH
- ポート : 22
- ソース :  (「セキュリティグループ-SSH」のグループID)
- VPC : 上記で作成したVPC

#### セキュリティグループ-Web
- 名前 : 任意 (例 : exment-sg-web)
- 説明 : 任意 (例 : exment-sg-web)

■作成後、インバウンドのルール追加
- タイプ : HTTP
- ポート : 80
- ソース : 0.0.0.0/0, ::/0

#### セキュリティグループ-MySQL
- 名前 : 任意 (例 : exment-sg-mysql)
- 説明 : 任意 (例 : exment-sg-mysql)

■作成後、インバウンドのルール追加
- タイプ : MYSQL/Aurora
- ポート : 3306
- ソース : (「セキュリティグループ-Web」のグループID)

#### セキュリティグループ-Redis
- 名前 : 任意 (例 : exment-sg-redis)
- 説明 : 任意 (例 : exment-sg-redis)

■作成後、インバウンドのルール追加
- タイプ : カスタムTCPルール
- ポート : 6379
- ソース : (「セキュリティグループ-Web」のグループID)


### AWS S3
- AWS S3のバケット作成、IAM作成を行います。  
※参考：[超簡単！LaravelでS3を利用する手順](https://qiita.com/tiwu_official/items/ecb115a92ebfebf6a92f)  
上記URLの「S3用のIAMを作成」「Bucketの作成」を実施してください  
※バケットは、「添付ファイル用」「バックアップファイル用」「プラグイン用」「テンプレート用」の4種類作成してくだい


### MySQL
> このマニュアルでは、Amazon RDSを使用したMySQLの作成設定を記載していますが、インスタンスを個別に作成し、MySQLをインストールし、各Webサーバーからアクセスする方法でも構築できます。

- エンジン : MySQL
- バージョン : 5.7.26
- インスタンス識別子 : 任意 (例 : exment-db)
- DB インスタンスクラス : 任意 (例 : db.t2.micro)
- ユーザー名、パスワード : 各自に設定
- Virtual Private Cloud : 先ほど作成したVP
- 追加の接続設定
    - VPC セキュリティグループ : セキュリティグループ-MySQL
    - アベイラビリティーゾーン : 指定なし


### Redis
> このマニュアルでは、ElastiCacheを使用したRedisの作成設定を記載していますが、インスタンスを個別に作成し、Redisをインストールし、各Webサーバーからアクセスする方法でも構築できます。

- クラスターエンジン : Redis
- 名前 : 任意 (例 : exment-redis)
- エンジンバージョンの互換性 : 5.0.5
- ポート : 6379
- ノードタイプ : 任意 (例 : cache.t2.micro)
- サブネットグループ : 新規作成
    - 名前 : 任意 (例 : exment-redis-subnet)
    - VPC : 先ほど作成したVPC
    - サブネット : すべての情報を入力
- セキュリティグループ : セキュリティグループ-Redis


### EC2
EC2インスタンスを2種類作成します。(踏み台サーバー、Webサーバー1台。2台目のWebサーバーは、後ほど複製します)

#### 踏み台サーバー
- AMI : Amazon Linux 2 AMI x86
- インスタンスサイズ : 任意 (例 : t2.nano)
- インスタンス数 : 1
- ネットワーク : 先ほど作成したVPC
- サブネット : 任意
- 自動割り当てパブリック IP : 有効
- タグ : キー-Name、値-任意 (例 : exment-step)
- セキュリティグループ : セキュリティグループ-SSH

#### Webサーバー
- AMI : Amazon Linux 2 AMI x86
- インスタンスサイズ : 任意 (例 : t2.micro)
- インスタンス数 : 1
- ネットワーク : 先ほど作成したVPC
- サブネット : パブリックサブネットA
- 自動割り当てパブリック IP : 有効
- タグ : キー-Name、値-任意 (例 : exment-web1)
- セキュリティグループ : セキュリティグループ-SSHプライベート、セキュリティグループ-Web


### Webサーバー-A

#### Webサーバー-Aへ接続
- SSHで、**踏み台サーバー**に接続します。

- EC2インスタンスに接続を行うために必要なkey(ここでは「exment_key.pem」とします)を、踏み台サーバーに転送します。

- 以下のコマンドを実行し、SSHキーの権限を変更します。

~~~
chmod 700 exment_key.pem
~~~

- 以下のコマンドを実行し、WebサーバーAへSSH接続を実施します。

~~~
ssh -i ~/exment_key.pem ec2-user@(WebサーバーAのプライベートIPアドレス)
~~~

#### Webサーバー設定

- 以下のコマンドを実行し、パッケージ更新、必要ソフトウェアのインストールなどを行います。

~~~
sudo yum -y update
sudo amazon-linux-extras install -y php7.4
sudo yum install -y httpd mysql
sudo yum -y install php-pecl-zip.x86_64 php-xml.x86_64 php-mbstring.x86_64 php-gd.x86_64
~~~

- 以下のコマンドを実行し、Apacheを起動、自動起動設定します。

~~~
sudo systemctl start httpd
sudo systemctl enable httpd
~~~

- swap領域を作成します。

~~~
sudo dd if=/dev/zero of=/swapfile bs=1M count=1024
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
~~~

- php7.4へのパスを通します。コマンドで、php7.4を実行できるようになります。

~~~
sudo ln -s /usr/bin/php74 /usr/bin/php
~~~

- composerをインストールします。
~~~
cd ~
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
~~~

- httpd.confを修正します。

~~~
sudo vi /etc/httpd/conf/httpd.conf

# 以下の記述を、末尾に追加

<VirtualHost *:80>
  DocumentRoot /var/www/exment/public
  <Directory /var/www/exment/public>
    Allow from all
    AllowOverride All
    Require all granted
  </Directory>
</VirtualHost>
~~~

- apacheを再起動します。
~~~
sudo systemctl restart httpd
~~~

- Exmentをサーバーに配置します。最新のExmentファイルをダウンロードし、展開します。  
~~~
cd /var/www
sudo wget https://exment.net/downloads/ja/exment.zip
sudo unzip exment.zip
sudo rm exment.zip -f
cd exment
~~~

- ファイルの許可設定を行います。以下のコマンドを実行します。

~~~
sudo usermod -a -G apache ec2-user
sudo chmod 2775 /var/www/exment && find /var/www/exment -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;
sudo chown -R ec2-user:apache /var/www/exment
~~~

- MySQL接続し、データベースを作成します。

~~~
mysql -u (MySQLのユーザー名) -p(パスワード名) -h (ホスト名)
// MySQLコマンドとして実行
create database exment;
~~~

- Redis接続と、AWS S3接続を行うために、以下のコマンドを実行します。

~~~
cd /var/www/exment
composer require league/flysystem-aws-s3-v3 ~3.0
composer require predis/predis
~~~

- .envファイルを編集します。

~~~

# すでにある設定値の変更
CACHE_DRIVER=redis #キャッシュの保存先を変更する場合。redisに書き換え
SESSION_DRIVER=redis #キャッシュの保存先を変更する場合。redisに書き換え
REDIS_HOST=127.0.0.1 #redisのホスト名
REDIS_PASSWORD=XXXXXX #redisのパスワード、設定した場合のみ記述
REDIS_PORT=6379 #redisのポート番号、6379でない場合のみ記述


# 新規に設定値追加
EXMENT_DRIVER_EXMENT=s3
EXMENT_DRIVER_BACKUP=s3
EXMENT_DRIVER_TEMPLATE=s3
EXMENT_DRIVER_PLUGIN=s3
AWS_ACCESS_KEY_ID=(S3アクセスキー)
AWS_SECRET_ACCESS_KEY=(S3シークレットアクセスキー)
AWS_DEFAULT_REGION=(S3リージョン。例：ap-northeast-1)
AWS_BUCKET_EXMENT=(添付ファイルで使用するAWS S3のバケット)
AWS_BUCKET_BACKUP=(バックアップで使用するAWS S3のバケット)
AWS_BUCKET_TEMPLATE=(テンプレートで使用するAWS S3のバケット)
AWS_BUCKET_PLUGIN=(プラグインで使用するAWS S3のバケット)

# ロードバランサーをHTTPS、ELBとEC2の通信をHTTPにする場合
ADMIN_HTTPS=true
~~~

- 手元のPCのブラウザで、作成したWebサーバーのIPアドレスを入力し、Exmentの初期インストールを完了させます。

#### Webサーバー 設定変更時
- Exmentアップデート後や、PHPファイルを更新した場合、設定値を変更した場合は、以下のコマンドを実行してください。

~~~
sudo systemctl restart php-fpm
~~~


#### WebサーバーのAMI設定

今後のWebサーバー冗長化などのために、WebサーバーのAMIを作成します。

- AWS ConsoleのEC2メニューにて、「アクション」→「イメージ」→「イメージの作成」をクリックします。

- イメージ名など(例 : exment-ami)を入力し、イメージを作成します。

- 数分後、左メニューの「AMI」より、先ほど設定したイメージが作成されています。


#### AMIより、WebサーバーB作成
- 左メニューの「AMI」より、上記で作成したイメージを選択し、「起動」ボタンをクリックします。

以下の設定で、Webサーバーを作成します。
  - インスタンスサイズ : 任意 (例 : t2.micro)
  - インスタンス数 : 1
  - ネットワーク : 先ほど作成したVPC
  - サブネット : パブリックサブネットB
  - 自動割り当てパブリック IP : 有効
  - タグ : キー-Name、値-任意 (例 : exment-web2)
  - セキュリティグループ : セキュリティグループ-SSHプライベート、セキュリティグループ-Web


### Amazon Elastic Load Balancing
- ロードバランサーの種類 : Application Load Balancer
- 名前 : 任意 (例 : exment-alb)
- リスナー : HTTPもしくはHTTPS
- VPC : 作成したVPC
- アベイラビリティーゾーン : すべて選択
- セキュリティグループ : セキュリティグループ-Web
- ターゲットグループ : 任意 (例 : exment-alb-tg)
- ターゲットグループインスタンス : 作成したWebサーバー2種類を選択肢、「登録済」に追加

設定完了後、ロードバランサーのDNS経由で、Webサーバーにアクセスできるようになります。