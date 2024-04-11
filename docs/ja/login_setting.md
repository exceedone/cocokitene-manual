# ログイン設定
Cocokiteneにログインを行う時の、各種ログイン設定を行います。

<!-- > SSOに関する設定・説明は、[こちら](/ja/login_sso)をご確認ください。 -->


<!-- ## 2段階認証
Exmentでは、2段階認証に対応しています。  
ユーザー情報を入力してログイン後、EメールもしくはGoogle認証システムによって送信されたコードを入力することで、Exmentにアクセスすることができるようになります。  
※2段階認証は、ログイン方式が「標準」「LDAP」の場合に利用できます。

- [2段階認証 システム設定](/ja/login_2factor_setting)
- [ログイン(2段階認証)](/ja/login_2factor) -->


## ログイン設定管理方法

- システム管理者が、以下のURLを入力します。  
http(s)://(CocokiteneのURL)/login </br>
※上記で記載の「システム設定変更」を行うことで、アクセスが出来るようになります。

![データ画面](img/login/login_method1.png)




- 一般的なユーザーが、以下のURLを入力します。  
http(s)://(CocokiteneのURL)</br>
※ユーザーはログイン方法が２つあり、Eメール・パスワードそれともウォレットアドレスです。</br>
※上記で記載の「システム設定変更」を行うことで、アクセスが出来るようになります。

![データ画面](img/login/login_method2.png)
<!-- - もしくは、メニューに「ログイン設定」を追加します。  
「管理者設定」 > 「メニュー」ページを開き、メニュー種類「システムメニュー」を選択すると、対象の「ログイン設定」が表示されますので、選択し、保存を行ってください。  
※「ログイン設定」は、デフォルトの設定ではメニューに表示されません。 -->

<!-- ![ログイン設定一覧](img/login/login_setting4.png)   -->

<!-- - 2段階認証の管理を行うには、下記のページの手順に従い、設定を行います。

    - [2段階認証 システム設定](/ja/login_2factor_setting) -->


### パスワード設定
ログインパスワードに関連するルールを設定します。   

#### 複雑なパスワード
- 設定がYESの場合、3種類以上の文字種（半角英大文字、半角英小文字、半角数字、半角記号）を含んだ８文字以上のパスワードを設定する必要があります。
- 初期値は「NO」です。

<!-- #### 有効日数
- 指定日数経過後に初めてログインした場合、パスワード変更画面に誘導されます。パスワードを変更してから、再度ログイン処理を行ってください。
- 有効日数を「0」にした場合、パスワードは無期限になります。
- 初期値は「0」です。 -->

#### 初回ログイン時にパスワードを変更させる
- YESにした場合、ユーザーがはじめてログインを行った際に、パスワードを変更させる画面を表示します。
- この設定をYESにした後で、新規登録、またはパスワードをリセットしたユーザーに対し、有効になります。
- この設定をYESにする前に新規登録などを行ったユーザーには、設定が反映されません。
- 初期値は「NO」です。

<!-- #### パスワードの履歴件数
- 最近使用したパスワードの再利用を制限します。新しいパスワードと直近～履歴件数分のパスワードを比較して同一だった場合はエラーにします。
- 履歴件数を「0」にした場合は比較対象外です。
- 初期値は「0」です。 -->

#### 注意

- <span class="small">「複雑なパスワード」は、ユーザー設定画面やパスワードのリセット画面などでユーザー自身がパスワードを設定する場合に適用されます。システム管理者が設定する場合は対象外です。</span>


<!-- ### その他
- v3.3.0未満からv3.3.0以上にアップデートした場合で、ユーザーコードに「ドット(.)」を含んだプロバイダでログインを行った場合、エラーとなることがあります。  
その場合、以下の対応を行ってください。
    - 「ユーザー」テーブルの「カスタム列設定」で、「ユーザーコード」列の編集画面に遷移する。
    - 「使用可能文字」オプションで、「ドット」にチェックを入れて保存する。 -->


- 既定の動作では、ログイン状態でブラウザを閉じた場合でも、一定時間（120分）経過するまでは、ログイン状態を維持します。  
v3.6.0以降で、ログイン状態でブラウザを閉じた場合に、自動的にログアウトにする場合には、「.env」ファイルを開き、以下のように追記を行ってください。  
※設定ファイルの編集方法について、詳細は[こちら](/ja/config)をご参照ください。
<!-- 
~~~
EXMENT_SESSION_EXPIRE_ON_CLOSE=true
~~~ -->




## パスワードリセット流れ
パスワードリセットは「パスワードをお忘れ」のURLで設定します

- システム管理者のURL：

~~~
http(s)://(CocokiteneのURL)/forgot-password
~~~


- ユーザーのURL：

~~~
http(s)://(CocokiteneのURL)/forgot-password-user
~~~

#### 引数：システム・ユーザー流れ

Eメール正しい場合：
- ##### email

対象のユーザーのEメールアドレスです。
![データ画面](img/login/login_setting1.png)

![データ画面](img/login/login_2factor1.png)

![データ画面](img/login/login_setting2.png)

![データ画面](img/login/login_2factor2.png)



- ##### password
変更対象のユーザーのパスワードです。

- ##### confirm password
入力したパスワードを改めて入力します。
※入力した新しいパスワードが一致しなければならないです。

![データ画面](img/login/login_setting3.png)

Eメール正しくない場合：

![データ画面](img/login/login_2factor_error.png)