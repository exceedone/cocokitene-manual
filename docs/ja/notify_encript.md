# 通知時の添付ファイルの暗号化
メールで通知を行う際に、添付ファイルを追加することができます。  
設定を行うことで、その添付ファイルを、システムで自動的に暗号化し、メール送信することができます。  
その設定方法を記載します。  

## Windows、Linux共通

- 以下の内容を、".env"に追加します。  

~~~
EXMENT_ARCHIVE_MAIL_ATTACHMENT=1
~~~

## Windows
- 暗号化されたzipファイルを作成するためには、7-zipをインストールします。  
[7-zip](https://sevenzip.osdn.jp/)

## Linux
- yumなど、OS側のパッケージコマンドで、zipをインストールします。

~~~
// CentOSの場合
yum install zip
~~~

これにて設定完了です。

