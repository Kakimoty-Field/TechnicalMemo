- 2019/01/22 タイトルを更新

## 知識材料 
- [enebular](https://www.enebular.com/)
- [Node-RED](https://nodered.org/)
- [heroku](https://www.heroku.com/)

## 前提条件
- enebular を heroku にデプロイしたい
- [enebular - Getting Started](https://docs.enebular.com/ja/GetStarted/) を体験済み
- [heroku Free プラン](https://jp.heroku.com/pricing)を使いたい

## 利用環境
- enebular 
- heroku

## 今回のゴール
heroku 無料プランは

>30 分間何も操作が行われなかった場合はスリープ状態に移行

という制限があります。
世の中には、これを回避する方法が溢れていましたが、heroku にデプロイした Node-Red 自身が、簡単に回避策を実装出来そうなので試しました。

# スリープ検証
heroku にデプロイしたアプリケーションを放置すると、スリープするのかどうかを検証しました。検証には [heroku cli](https://devcenter.heroku.com/articles/heroku-cli) を使用します。

`heroku ps -a [アプリ名]` で、デプロイ済みアプリケーションの状況が確認できます。

アプリケーションが開始した時間と、今までの経過時間が表示されます。また、補助情報として heroku のログも見ておきます。
![実装前起動](https://dl.dropboxusercontent.com/s/4tn1p7ls0f9pnv4/190122-01_01.png)

３０分以上放置した後、再び `heroku ps` で、アプリケーションの状況を確認します。

![実装前スリープ](https://dl.dropboxusercontent.com/s/x7ecwtypivvgygu/190122-01_02.png)

放置後、約３０分でスリープ状態 [idle] になりました。

# 実践
## flow 作成
![AllView](https://dl.dropboxusercontent.com/s/yivrr0azj39ehds/190122-01_06.png)

heroku にデプロイした時にスリープしてほしくないフローの端に、上記赤枠のノードを配置し、接続します。

[inject] -> [http request] -> [(debug) 無くても可]

### [inject] ノード設定
「繰り返しフィールド」を、下記のように設定
- 指定した時間間隔
- 時間間隔 `15分`

![inject-node](https://dl.dropboxusercontent.com/s/tz2rpquut4kmq28/190122-01_07.png)

### [http request] ノード
`URL` フィールドに、heroku にデプロイしたアプリケーションの URL を指定します
![http-request-node](https://dl.dropboxusercontent.com/s/a15g6bq0fhdb4l0/190122-01_08.png)

## 実行と結果
【１】作成した flow を heroku にデプロイします。デプロイ後に `heroku ps` で、アプリケーションの動作状況を確認します。
![実装後起動](https://dl.dropboxusercontent.com/s/62egs2g10z8ul3z/190122-01_03.png)

【２】３０分以上放置した後、再び `heroku ps` で、アプリケーションの状況を確認します。

![実装後スリープ](https://dl.dropboxusercontent.com/s/88b4p8ofyn9xtb6/190122-01_04.png)

【３】３０分以上経過しても、スリープ状態 [idle] に遷移しませんでした。

![実装後スリープ２](https://dl.dropboxusercontent.com/s/8sq1b3t23ab8gvf/190122-01_05.png)

【４】４時間経過までは見届けました。

## まとめ
heroku addons を利用すればどんなアプリでもスリープ回避できますが、node-red だと自前でお手軽にスリープ回避できることを体験できました。ちょっと応用すれば、自前で死活監視もできるかと思います。
