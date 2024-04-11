# メール送信設定
Exmentからメール送信を行うための設定を行います。  
また、設定後のテストメール送信手順も記載します。  

> ※「メールが届かない」「メールが迷惑メールに分類される」といった、メール送信機能については、各サーバーや環境による部分が大きいため、お問い合わせの対象外とさせていただきます。  

## 概要
- Exmentでは、システムからメールを送信することがあります。  
例：
    - データ更新通知
    - 2段階認証

- メール送信処理を行うには、SMTP設定などを行う必要があります。  
ここでは、メール送信を行うための設定手順を記載します。

- メール送信の設定方法は2種類あります。
    - [画面から設定(既定)](#設定方法-画面から設定) :   
    システム設定画面でSMTP設定を入力することで、メール送信設定を行います。シンプルなSMTP設定でメール送信を行う場合に利用できます。
    
    - [configファイルやenvファイルで設定](#設定方法-configファイルやenvファイルで設定)：  
    Amazon SESなど、SMTPでない方法でメール設定を行う場合、サーバーで設定ファイル編集や、composerなどを利用し設定を行います。※上級者向けです。

### 設定方法-画面から設定
- システム管理者が、システム設定画面にアクセスします。

- 以下の設定を入力します。
![システム設定画面](img/system_setting/system_setting_mail.png)  

※SMTP設定例(Xserverの場合) ： [こちら](https://www.xserver.ne.jp/manual/man_mail_setting.php)  

※SMTP設定例(さくらインターネットの場合) ： [こちら](https://help.sakura.ad.jp/hc/ja/articles/206054132--%E3%83%A9%E3%82%A4%E3%83%88-%E3%82%B9%E3%82%BF%E3%83%B3%E3%83%80%E3%83%BC%E3%83%89-%E3%83%97%E3%83%AC%E3%83%9F%E3%82%A2%E3%83%A0-%E3%83%A1%E3%83%BC%E3%83%AB%E3%83%9C%E3%83%83%E3%82%AF%E3%82%B9-%E3%83%A1%E3%83%BC%E3%83%AB%E3%82%BD%E3%83%95%E3%83%88%E3%81%AE%E4%B8%80%E8%88%AC%E7%9A%84%E3%81%AA%E8%A8%AD%E5%AE%9A%E6%96%B9%E6%B3%95)  


### 設定方法-configファイルやenvファイルで設定
Exment標準では、メール設定は、画面の「システム設定」で登録した値を使用しています。  
しかし、Amazon SESなど、SMTPでない方法でメール設定を行いたい場合もあります。  
その場合は、Laravel標準の設定方法である、「.env」ファイルに設定を記入する方法を使用できます。  
この設定方法を使用する場合、下記の設定を行ってください。※上級者向けです。


- .envファイルを開き、以下の設定値を入力します。

~~~
EXMENT_MAIL_SETTING_ENV_FORCE=true
~~~

- その後、各種Laravelのメール送信ドライバを設定してください。

- また、以下の設定を.envに入力することをおすすめします。

~~~
MAIL_FROM_ADDRESS=(送信元アドレス)
MAIL_FROM_NAME=(送信元に表示される名前)
~~~

## SPFレコード設定
- 送信メールが迷惑メールになる場合、多くはドメインの「SPFレコード」の設定の不備になります。ドメイン設定で、SPFレコードを登録してください。  
[参考1](https://sendgrid.kke.co.jp/blog/?p=3509)  
[参考2](https://salt.iajapan.org/wpmu/anti_spam/admin/operation/information/spf_i01/)  


## テストメール送信
- 上記のメール送信設定を利用して、テストメール送信を実行することができます。  
画面から実行、またはコマンドから実行を行うことができます。

### 画面から実行
- システム管理者が、システム設定画面にアクセスします。

- 「テストメール送信」項目で、メール送信先を入力後、［テストメール送信］をクリックします。

![システム設定画面](img/system_setting/system_setting_mail_testmail.png)  

※処理の都合上、ページ上部の「システムメール設定」を保存する必要があります。一度保存してから、テストメールを送信してください。


### コマンドから実行
- プロジェクトのルートフォルダで、以下のコマンドを入力してください。

~~~
php artisan exment:notifytest --to=(メール送信先)
~~~

### 送信エラー時
- パス「storage/logs/laravel.log」に、エラーログが出力されるので、ご確認ください。

