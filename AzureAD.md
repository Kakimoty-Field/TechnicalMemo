# Microsoft ID + Node-RED で ログイン画面を作る

## 知識材料 
- [Node-RED](https://nodered.org/)
- [Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー](https://docs.microsoft.com/ja-jp/azure/active-directory/develop/v2-oauth2-auth-code-flow#request-an-authorization-code)

## 利用環境
- [Node-RED]
- IBM Cloud (https のURLが必要なので、IBM Cloud で環境を用意するとお手軽だと思います。)

## 今回のゴール
自組織のMicrosoft アカウントを利用してログインできる Webアプリケーションのログイン部分を、Node-RED で実装する。

# 実践概要
## Microsoft Application ID取得
Azure にログイン後、左メニューから `Azure Active Directoryメニュー` をクリック。
これ以降は、`Azure Active Directory (AzureAD)` 内のメニューを操作する。

### アプリケーションを登録
#### アプリケーション作成
自組織の `App registrationsメニュー` をクリックする。

App registrations ダイアログの上部　「＋ New registration」をクリックする。

![App registrations](https://dl.dropboxusercontent.com/s/6omtbvapctnvv8t/20190601-01-02.png)

`Register an application` ダイアログでは、`Name` プロパティに、このアプリケーションの名前を入力する。（例では `TestLogin`)
入力したらダイアログ下部の 「Register」 をクリックする。
![App registrations](https://dl.dropboxusercontent.com/s/zzgr2in4d5us1y3/20190601-01-03.png)

アプリケーションの作成が完了したら、アプリケーションのプロパティ画面から `Application (client) ID` と `Directory (tenant) ID` をメモする。
![ClientID](https://dl.dropboxusercontent.com/s/e7rwz50w8u0njtf/20190602-01-07.png)

#### RedirectURL 登録
作成したアプリケーションのダイアログで`Authentication`メニューをクリックし、`RedirectURL`に、これから作成するWebアプリケーションのトップページのURL を入力する。
入力が完了したら、ダイアログ上部の「Save」をクリックする。

![RedirectURL](https://dl.dropboxusercontent.com/s/lxg6fnvwmsqty2g/20190601-01-04.png)
#### Client secrets 作成
作成したアプリケーションのダイアログで`Certificates & secrets`メニューをクリックし、ダイアログ下方の `Client secrets` から「＋New client secret」ボタンをクリックし、新しい client secret を作成し、メモする。（有効期限は、適宜選択する）

![client secrets](https://dl.dropboxusercontent.com/s/y3nm82962p4lvhi/20190601-01-05.png)

## Node-RED フロー作成

`(1)ログイン画面へ` と `(2)Token 取得` の Function ノード内の、下記を適切な値に変更する。

- {Directory (tenant) ID}
- {Application (client) ID}

- {RedirectURL};
- {client secret}

![Node-RED Flow](https://dl.dropboxusercontent.com/s/wvyoq3tob1wgtfg/20190601-01-06.png)
```
[{"id":"946738f8.0aea78","type":"tab","label":"Microsoft ID","disabled":false,"info":""},{"id":"f2fd2be5.f839b8","type":"http in","z":"946738f8.0aea78","name":"","url":"/msapp","method":"get","upload":false,"swaggerDoc":"","x":90,"y":60,"wires":[["f936e317.9c61e"]]},{"id":"80013dcd.a758f","type":"http in","z":"946738f8.0aea78","name":"","url":"/mslogin","method":"post","upload":false,"swaggerDoc":"","x":90,"y":140,"wires":[["77eca14a.70ea5","7bd7564c.af5b58"]]},{"id":"f936e317.9c61e","type":"function","z":"946738f8.0aea78","name":"(1)ログイン画面ヘ","func":"var tenant = {Directory (tenant) ID};\nvar clientid = {Application (client) ID};\nvar redirectURL = {RedirectURL};\n\nmsg.url = \"https://login.microsoftonline.com/\" + tenant + \n \"/oauth2/authorize\";\n \nmsg.method = \"GET\";\nmsg.statusCode = 303;\nmsg.headers = {\n    location : \n    msg.url + \n    \"?client_id=\" + clientid + \n    '&response_type=code' + \n    '&redirect_uri=' + encodeURIComponent(redirectURL) + \n    '&response_mode=form_post' + \n    '&state=nagi_hisakawa'\n};\nmsg.payload = {};\nreturn msg;\n","outputs":1,"noerr":0,"x":330,"y":60,"wires":[["2a2e40a7.09177"]]},{"id":"2a2e40a7.09177","type":"http response","z":"946738f8.0aea78","name":"","statusCode":"","headers":{},"x":670,"y":60,"wires":[]},{"id":"77eca14a.70ea5","type":"debug","z":"946738f8.0aea78","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":310,"y":220,"wires":[]},{"id":"7bd7564c.af5b58","type":"function","z":"946738f8.0aea78","name":"(2)Token 取得","func":"var tenant = {Directory (tenant) ID};\nvar clientid = {Application (client) ID};\nvar redirectURL = {RedirectURL};\nvar secret = {client secret};\n\nmsg.url = \"https://login.microsoftonline.com/\" + tenant + \"/oauth2/token\";\n\nmsg.method = \"POST\";\nmsg.headers = \n{\n    'Content-Type': \"application/x-www-form-urlencoded\"\n}\n\nmsg.payload = {\n  'grant_type' : \"authorization_code\",\n  'client_id' : clientid,\n  'code' : msg.payload.code,\n  'redirect_uri' : redirectURL,\n  'client_secret': secret,\n  'resource' : clientid\n};\nreturn msg;\n","outputs":1,"noerr":5,"x":320,"y":140,"wires":[["22ae285e.090488"]]},{"id":"22ae285e.090488","type":"http request","z":"946738f8.0aea78","name":"","method":"use","ret":"obj","url":"","tls":"","x":490,"y":140,"wires":[["2c257eeb.8c3312"]]},{"id":"2c257eeb.8c3312","type":"function","z":"946738f8.0aea78","name":"(3)JWT 展開","func":"var base64Url = msg.payload.id_token.split('.')[1];\nvar base64 = base64Url.replace('-', '+').replace('_', '/');\nvar bb = new Buffer(base64, 'base64').toString();\nvar c = JSON.parse(bb);\nmsg.payload = c;\n//msg.payload.user = c.name;\n\nreturn msg;\n","outputs":1,"noerr":0,"x":490,"y":180,"wires":[["2a2e40a7.09177"]]}]
```

## 実行と結果

`http://ホスト名/msapp` にアクセスすると、Microsoft ログイン画面へリダイレクトされます。

ログインに成功すると、ログインしたユーザ情報がブラウザに表示されます。

