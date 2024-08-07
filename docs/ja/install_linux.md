# Linuxによる環境構築
本ページでは、LinuxでCocokiteneを構築する手順を記載します。  
Webサーバーのインストールをはじめとして、完全に新規にインストールを行う手順となります。

## 環境
本ページでは、以下の内容で構築を行っております。  
- CentOS Stream 9
- Node 16.20.x
- Git 2.43.5
- MySQL 8.x.x
- WGet 1.21.1

## 注意点

- ご利用の環境やバージョンなどにより、本手順では正常に設定できない場合がございます。ご了承ください。

- 本手順では、CocokiteneをLinuxで動作させるための手順のみの記載となります。  
SSHやデータベース作成、Linuxコマンドなど、一般的なIT系のナレッジについては記載致しておりません。ご了承ください。  

- **本手順では、管理者アカウントで実行している前提です。管理者アカウントでないユーザーが実行する場合、"sudo"を頭に付与してください。**

<!-- - インストール時にエラーが発生した場合、[トラブルシューティング](/ja/troubleshooting)をご参照ください。 -->


## Linuxによるインストール手順

### Webサーバー
- 事前準備として現在のパッケージのアップグレード、ならびに必要ライブラリのインストールを行います。  

~~~
dnf -y upgrade
dnf install -y wget firewalld unzip git
~~~


- 必要なEPELをインストールします。

~~~
dnf install -y epel-release
~~~

- Nodeパッケージをインストールします。

~~~
dnf install -y gcc-c++ make
wget https://rpm.nodesource.com/setup_16.x | sudo -E bash
dnf install -y nodejs
~~~

- Nodeのバージョンが16.20.xになっていることを確認します。

~~~
node -v
npm -v
~~~

- SQLとアカウント設定
SQL設定
~~~
wget https://dev.mysql.com/get/mysql84-community-release-el9-1.noarch.rpm
rpm -ivh mysql84-community-release-el9-1.noarch.rpm
dnf install mysql-server
systemctl start mysqld
systemctl status mysqld
~~~

SQLアカウント設定
~~~
grep 'password' /var/log/mysqld.log
mysql_secure_installation
mysql -u root -p
~~~

SQLスキーマ作成
~~~
CREATE SCHEMA cocokitene;
SHOW GLOBAL VARIABLES LIKE 'PORT';
~~~


- ファイアウォール設定を行います。  
※ご希望のセキュリティ設定に合わせ、設定内容を変更してください。

~~~
systemctl start firewalld
systemctl enable firewalld
firewall-cmd --add-service=http --zone=public --permanent
firewall-cmd --add-service=https --zone=public --permanent
systemctl reload firewalld
~~~

- FE設定
~~~
git clone https://xx.xx/cocokitene-fe.git
cd ~
touch .env.local
vi .env.local
~~~

env.localファイル設定
~~~
NEXT_PUBLIC_API_ENDPOINT=
NEXT_PUBLIC_API_SOCKET=
NEXT_PUBLIC_TRANSACTION_LINK=
NEXT_PUBLIC_PROXY_CONTRACT=
~~~

FE実施
~~~
npm run trans
npm run build
npm run start
~~~

 手元のPCのブラウザで、作成したWebサーバーのIPアドレスを入力し、CocokiteneのFEの初期インストールを完了させます。


- FE設定
~~~
git clone https://xx.xx/cocokitene-be.git
cd ~
touch .env
vi .env
~~~

envファイル設定
~~~
# node env
NODE_ENV=

# network env
NETWORK_ENV=

# database
DB_HOST=
DB_PORT=
DB_NAME=
DB_USER=
DB_PASS=
DB_TYPE=
DB_LOGGING=
DB_SYNC=

# api
API_PORT=
API_PREFIX=
ACCESS_JWT_SECRET_KEY=
REFRESH_JWT_SECRET_KEY=
ACCESS_TOKEN_EXPIRE_IN_SEC=
REFRESH_TOKEN_EXPIRE_IN_SEC=



# email
EMAIL_HOST=
EMAIL_PORT=
EMAIL_AUTH=
EMAIL_USER=
EMAIL_PASSWORD=


# email cc
CC_EMAILS=


# aws s3ZN
S3_ACCESS_KEY=
S3_SECRET_ACCESS_KEY=
S3_REGION=
S3_BUCKET_NAME=
S3_URL_EXPIRES_IN_SEC=

# secreat key to encode and decode password
PASSWORD_SECRET_KEY=
PASSWORD_SECRET_KEY_USER=
#System-admin
SYSTEM_ADMIN_ACCESS_JWT_SECRET_KEY=
SYSTEM_ADMIN_REFRESH_JWT_SECRET_KEY=
SYSTEM_ADMIN_ACCESS_TOKEN_EXPIRE_IN_SEC=
SYSTEM_ADMIN_REFRESH_TOKEN_EXPIRE_IN_SEC=


#Maximum number of transactions per call function on the blockchain
MAXIMUM_NUMBER_TRANSACTION_PER_CALL_FUNC_BLOCKCHAIN=100


# crawler

ADMIN_PRIVATE_KEY=
ADMIN_ADDRESS=

#set time run cronjob, run according to the operating system time where the server is located
CRON_JOB_HANDLE_ENDED_MEETING=
CRON_JOB_HANDLE_PENDING_TRANSACTION=
CRON_JOB_CRAWL_MEETING_EVENT=
CRON_JOB_HANDLE_DATA_AFTER_EVENT_SUCCESSFUL_CREATE_MEETING=
CRON_JOB_HANDLE_DATA_AFTER_EVENT_SUCCESSFUL_UPDATE_PROPOSAL_MEETING=


#FE
FE_PORT=
IP_ADDRESS=
LANGUAGE_EN=en
LANGUAGE_JA=ja

#Number phone in landingpage to contact when user register service
NUMBER_PHONE=

#log
FOLDER_LOG=
#chat
CHAT_PUBLIC=
CHAT_PRIVATE=
CC_EMAILS=
~~~

envファイルの中身をすべていれてから、ターミナルで以下のコマンド実施、BEの初期を完了させました。
~~~
npm run seed
npm run build
npm run start
~~~


