# AWSによる環境構築-シングル構成
本ページでは、Amazon Linux2でExmentを構築する手順を記載します。  
Webサーバーのインストールをはじめとして、完全に新規にインストールを行う手順となります。  
また、本手順では**シンプルなWebサーバー1台構成としております。**  
※冗長化したAWS構成の環境構築方法は、[こちら](/ja/install_aws)をご確認ください。

## 環境
本ページでは、以下の内容で構築を行っております。  
- Amazon Linux 2
- Apache 2.4.53
- PHP 8.2.x
- MySQL 8.0.x


## 注意点

- ご利用の環境やバージョンなどにより、本手順では正常に設定できない場合がございます。ご了承ください。

- 本手順では、ExmentをLinuxで動作させるための手順のみの記載となります。  
SSHやデータベース作成、Linuxコマンドなど、一般的なIT系のナレッジについては記載致しておりません。ご了承ください。  

- インストール時にエラーが発生した場合、[トラブルシューティング](/ja/troubleshooting)をご参照ください。


## Linuxによるインストール手順

### MySQL(任意)
任意の設定内容になります。MySQLサーバーを別に構築すると想定し、MySQLをインストールする場合の手順です。  
※AWS RDSサービスを使用する場合など、外部サービスを使用する場合は、MySQLサーバー側の手順は不要です。  
※[こちら](/ja/install_mysql)に移動しました。

### Redis(任意)
※[こちら](/ja/additional_session_cache_driver)に移動しました。


### Webサーバー

- 事前準備としてパッケージ更新を行います。

~~~
sudo yum -y update
~~~

- インストール可能なPHPのバージョンを確認します。  
※PHP8.2以外のバージョンが有効(enabled)になっている場合は無効化しておきましょう。  

~~~
sudo amazon-linux-extras | grep php
~~~

- 「php8.2」を確認できたら、インストールを行います。

~~~
sudo amazon-linux-extras install -y php8.2
~~~

- その他、必要ライブラリのインストールを行います。

~~~
sudo yum install -y httpd git
sudo yum -y install php-pecl-zip.x86_64 php-xml.x86_64 php-mbstring.x86_64 php-gd.x86_64 php-sodium.x86_64 php-dom.x86_64
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

- composerをインストールします。

~~~
cd ~
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
~~~

- php.iniに、必要な記述・編集を行います。  
特に、[PHP設定値変更]（/ja/additional_php_ini）で、メモリ使用量の変更・タイムアウト時間変更などを行う場合は、こちらの設定を変更してください。

~~~
sudo vi /etc/php.ini
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

- ファイルの許可設定を行います。以下のコマンドを実行します。

~~~
# ec2-userユーザーを、apacheグループに追加する
sudo usermod -a -G apache ec2-user
~~~

- **(MySQLを同サーバーにインストールしない場合のみ)**  
MySQLを同サーバーにインストールしない場合でも、mysqlコマンドを実行できるように、mysqlクライアントをインストールする必要があります。  
※MySQLを同サーバーにインストールする場合は、この手順を飛ばしてください。

~~~
sudo rpm -ivh http://dev.mysql.com/get/mysql80-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

# こちらを実施時して、mysql-community-clientが存在するかを確認します
sudo yum search mysql-community-client
# 上記コマンドで「Error: Unable to find a match: mysql-community-client」のような存在しない旨のメッセージが出た場合は、先に下記のコマンドを実施してください
sudo yum -y module disable mysql

# mysql-community-clientインストール
sudo yum -y install mysql-community-client
~~~

- Exmentをサーバーに配置します。最新のExmentファイルをダウンロードし、展開します。  
※こちらはかんたんインストールの手順です。手動インストールの場合、[こちら](/ja/quickstart_manual)を参考に配置してください。

~~~
cd /var/www
sudo wget https://exment.net/downloads/ja/exment.zip
sudo unzip exment.zip
sudo rm exment.zip -f
cd exment
~~~

- ファイルの許可設定を行います。以下のコマンドを実行します。

~~~
# 最低限の権限を追加する
sudo chmod 0775 /var/www/exment
sudo chown -R ec2-user:apache /var/www/exment
sudo setfacl -m u:apache:rwx -R /var/www/exment

# 以下のコマンドを実行し、フォルダの権限を付与する。1もしくは2を実施する
# 1. かんたんインストールの場合
sudo php artisan exment:setup-dir --easy=1
# 2. 手動インストールの場合
sudo php artisan exment:setup-dir
~~~


#### Webサーバー 設定変更時
- Exmentアップデート後や、PHPファイルを更新した場合、設定値を変更した場合は、以下のコマンドを実行してください。

~~~
sudo systemctl restart php-fpm
~~~



## PHPバージョンアップ時の対応
PHPのバージョンを変更する場合、以下の手順でバージョンアップを行ってください。  
※バージョンアップ作業中は、Exmentにアクセスできなくなります。  
※下記の例は、PHP8.0からPHP8.2へアップデートするための手順です。  
※Amazon Linux 2のExtras Library(amazon-linux-extras)を用いて、PHPのインストールを行っている前提です。  
※環境や導入時期、バージョンやインストール方法によって、バージョンアップ方法は異なる場合があります。  

- amazon-linux-extras パッケージがインストールされていることを確認します。  

~~~
which amazon-linux-extras
# インストールされていない場合は下記のコマンドでインストールしてください。
# sudo yum install -y amazon-linux-extras
~~~

- PHPのバージョンとExtras Libraryのトピックを確認します。  
※PHPのバージョンが8.0.Xであること。PHP8.0のトピックがenabledであること、PHP8.2のトピックが存在することを確認してください。  

~~~
php -v
amazon-linux-extras | grep php
~~~

- PHP8.0のトピックを無効にします。  

~~~
sudo amazon-linux-extras disable php8.0
~~~

- PHPフォルダを空にします。  

~~~
sudo yum -y remove php\*
~~~

- PHP8.2のトピックを有効化後にインストールします。  

~~~
sudo amazon-linux-extras enable php8.2
sudo amazon-linux-extras install -y php8.2
~~~

- PHPの拡張機能をインストールします。  

~~~
sudo yum -y install php-xml.x86_64 php-mbstring.x86_64 php-gd.x86_64 php-sodium.x86_64 php-dom.x86_64
~~~

- PHPのバージョンが8.Xになっていること、PHP8.0のトピックがdisabled、PHP8.2のトピックがenabledになっていることを確認します。  

~~~
php -v
amazon-linux-extras | grep php
~~~

- 以下のコマンドを実行してください。

~~~
sudo systemctl restart php-fpm
sudo systemctl restart httpd
~~~
