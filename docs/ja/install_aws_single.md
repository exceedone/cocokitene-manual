# AWSによる環境構築-シングル構成
本ページでは、Cocokiteneを構築する手順を記載します。  
Webサーバーのインストールをはじめとして、完全に新規にインストールを行う手順となります。  
また、本手順では**シンプルなWebサーバー1台構成としております。**  
※冗長化したAWS構成の環境構築方法は、[こちら](/ja/install_aws)をご確認ください。

## 環境
本ページでは、以下の内容で構築を行っております。  
- AWS Fargate
- Amazon S3
- Amazon RDS


## 注意点

- ご利用の環境やバージョンなどにより、本手順では正常に設定できない場合がございます。ご了承ください。


## インストール手順

### AWS RDS
[Relational Database Service]と言います。
webサービスを作るにあたって、サービスをローカルで作成するとき同じパソコンの中にDBをインストールするように、
「AWS Fargateを建てればその中にDBをインストールしてそこで運用する」こともできます。
- [RDS設定](/ja/install_aws_rds)  
→AWS RDS設定マニュアル詳細

### AWS Fargate
AWS上のサーバーレスコンテナサービスです。
Fargateを使うことで、コンテナの実行に集中でき、インフラの管理コストを削減できます。マイクロサービスやサーバーレスアーキテクチャの実現に適したサービスです。

- [Fargate設定](/ja/install_aws_fargate)  
→AWS Fargate設定マニュアル詳細

### Amazon S3
Amazon S3 は、バケットとオブジェクトを操作することで、使用を開始できます。バケット とは、オブジェクトのコンテナのことです。オブジェクト とは、ファイルと、そのファイルを記述している任意のメタデータのことです。

- [S3Bucket設定](/ja/install_aws_s3)  
→AWS S3Bucket設定マニュアル詳細

