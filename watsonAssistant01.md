## 目標
チャットボットを利用して、ユーザから会話形式で情報を入力してもらいたい

## 知識材料 
- [Watson Assistants](https://www.ibm.com/watson/jp-ja/developercloud/conversation.html)
- [Node-RED](https://nodered.org/)
- ([LINE Messaging API](https://developers.line.biz/en/services/messaging-api/))

## 利用環境
- [Node-RED]

## 今回のゴール
LINE アプリを通してサーバ処理を呼び出し、IBM Assistans で会話させる。

| |環境|
| ---- | ---- |
|クライアント|LINE| 
|アプリケーションサーバ|Node-RED|
|自然言語対話| IBM Assistants 


会議室を予約するシステムのUIとして、ボットを利用しユーザに下記を入力させる
- 日付
- 開始時間
- 終了時間
- 場所

# 実践概要
## Dialog(対話) 作成
![Dialog Flow](https://dl.dropboxusercontent.com/s/k70vuazw0ufo3io/190118-02-01.png)

## Node-RED フロー
まだ
## 実行と結果
![結果](https://dl.dropboxusercontent.com/s/q8nkxfsyvqo7ny3/190107-01.png)

## まとめ
ユーザから、処理に必要な情報を入力させるような「会話」を作成し、それに対するサーバ処理を実装出来そうな予感がしてきた。
