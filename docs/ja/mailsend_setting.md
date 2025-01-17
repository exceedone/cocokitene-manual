# メール送信設定
Cocokiteneからメール送信を行うための設定を行います。  
また、設定後のテストメール送信手順も記載します。  

> ※「メールが届かない」「メールが迷惑メールに分類される」といった、メール送信機能については、各サーバーや環境による部分が大きいため、お問い合わせの対象外とさせていただきます。  

## 概要
- Cocokiteneでは、システムからメールを送信することがあります。  
例：
    - サービス登録
    - 新規会社作成
    - 新規アカウント作成
    - 株主総会に招待状
    - 取締役会に招待状を送信
    - サービス期限の注意メール
    - プラン更新リクエストの受け付けのメール
    - プラン更新完了のお知らせのメール
    - サービス停止のお知らせのメール

- メール送信処理を行うには、SMTP設定などを行う必要があります。  
ここでは、メール送信を行うための設定手順を記載します。

### 設定方法-envファイルで設定
「.env」ファイルに設定を記入する方法を使用できます。  
この設定方法を使用する場合、下記の設定を行ってください。※上級者向けです。


- .envファイルを開き、以下の設定値を入力します。

~~~
EMAIL_HOST= xxxx
EMAIL_PORT= xxxx
EMAIL_AUTH= FALSE
~~~

- また、以下の設定を.envに入力することをおすすめします。

~~~
EMAIL_USER= (サーバーメール先)
EMAIL_PASSWORD=　（サーバーメールパスワード）
CC_EMAILS=　（サービス登録メールのCC（複数可能））
~~~



