# デレマスアイドル人気調査　Twitter + Watson Assistant + Firebase + enebular(infomotion) 

# こんなキャンペーンしてた
```
アイマス公式アカウント(@imas_official)を
フォロー＆ツイートをRTで等身大アイドルパネルを9名にプレゼント

応募締切はツイート内の日付ではなく、2/11（月祝）いっぱい！
希望がある場合は、RT後に「●●ちゃんがほしいです！」と元ツイートにコメントをつけてね
```
[キャンペーン元ツイート](https://twitter.com/imas_official/status/1092605512250585088)


## ふと思う
> ... 〇〇ちゃんをカウントしたら、人気ランキングがつくれちゃうんじゃなかろうか。

## やってみた

![answer](https://dl.dropboxusercontent.com/s/erih5qx8iwcylns/20190207-01_00.png)
*２月５日　１１時〜１７時 くらいの範囲*


## 動機
- Twitter API あんまり使ったことがない (`statuses/user_timeline.json`  くらい)
- watson をつかってみたい
- データの可視化って楽しそう。
- グラフをお気軽に作るツールを最近知った (`infomotion`)

# 実践

## 知識材料 
### 利用サービス
- [Twitter API](https://developer.twitter.com/)
- [Watson Assistants](https://www.ibm.com/watson/jp-ja/developercloud/conversation.html)
- [Firebase](https://firebase.google.com/?hl=ja)
- [enebular](https://www.enebular.com/ja/index.html)

### プログラム実行環境
- [node.js](https://nodejs.org/ja/)
- [Node-RED](https://nodered.org/)

## 構成
![info](https://dl.dropboxusercontent.com/s/y3l7y41we9kesl3/20190207-01_01.png)



## 各セクションの概要

### Twitter から対象データ取得
`search/tweets.json` で、特定のアカウントに対するコメントのみを検索("`q=to%3Aimas_official`")
```
function search(lastId)
{
	var postOption =
	{
		'hostname'	: "api.twitter.com",
		'port'		: 443,
		'path'		: "/1.1/search/tweets.json"
						+ "?q=" + "to%3Aimas_official"
						+ "&result_type=recent" // 最近の
						+ "&count=100" ,
		'method'	: "GET",
		'headers'	:
			{
				'Authorization'	: "Bearer " + token, // APIトークンは別枠で取得
			}
	};
	// RT コメントを途中まで読んでいたら、続きから読み込む
	if( lastId ) 
		postOption.path += "&max_id=" + lastId;

	// API コール
	var req = https.request(postOption, function(res)
		{
			var ret = '';
			res.on('data', function(buf)
				{
					ret += buf;
				});

			res.on('end', function()
				{
					var currentTweet = JSON.parse(ret);
					var id = "0";
					var found = false;
					// 取得出来たコメントの分、ループ
					currentTweet.statuses.forEach((x) => 
					{
						if( ! /RT/.test(x.text) && repId !== x.id_str ) {
							// 対象ツイートへのリプライだったら、CSV に吐き出す
							if( x.in_reply_to_status_id_str === "1092605512250585088" ) {
								// コメント中の改行コードを削除
								var tx = x.text.replace(/\n/g, ""); 
								fs.writeFileSync("a.csv",  
									(new Date(x.created_at)).getTime()  
										+ "," + x.id_str 
										+ "," +  tx + "\r\n"
									, { flag : "a" });
									
								found = true;
							}
						}
						id = x.id_str;
					});

					if( ! found ) 
						return ;
					else 
						search(id);	// 対象コメントが取得出来ているうちは、再起コール
				});
		});
}

```
### アイドル名抽出 (watson assistant)

「このアイドルは、こういう呼ばれ方 してるよー」というのを、watson に教えてみた。

![watson](https://dl.dropboxusercontent.com/s/uw7fjb7ztiimimm/20190207-01_03.png)



### 抽出結果をデータストアに保存 (Firebase RealtimeDatabase)

![](https://dl.dropboxusercontent.com/s/nqv8d182ouyvo2q/20190207-01_02.png)



### データの可視化 (enebular infomotion)