<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [目標](#目標)
  - [知識材料](#知識材料)
  - [利用環境](#利用環境)
  - [今回のゴール](#今回のゴール)
- [環境準備](#環境準備)
  - [node-red-contrib-model-asset-exchange 追加](#node-red-contrib-model-asset-exchange-追加)
  - [node-red-contrib-browser-utils 追加](#node-red-contrib-browser-utils-追加)
  - [node-red-contrib-image-output 追加](#node-red-contrib-image-output-追加)
- [image-caption-generator (画像概要取得) を試す](#image-caption-generator-画像概要取得-を試す)
  - [サンプル読み込みとデプロイ](#サンプル読み込みとデプロイ)
  - [サンプルの実行](#サンプルの実行)
- [object-detector (物体認識) を試す](#object-detector-物体認識-を試す)
  - [サンプル読み込みとデプロイ](#サンプル読み込みとデプロイ-1)
  - [サンプルの実行](#サンプルの実行-1)
- [まとめ](#まとめ)

<!-- /code_chunk_output -->


# 目標
node-red-contrib-model-asset-exchange で物体認識した結果を取得します

## 知識材料 
- [Node-RED](https://nodered.jp/)
- [enebular](https://docs.enebular.com/ja/)
- [node-red-contrib-model-asset-exchange](https://flows.nodered.org/node/node-red-contrib-model-asset-exchange)

## 利用環境
- enebular

## 今回のゴール
node-red-contrib-model-asset-exchange ノードを利用して、「画像の概要」と「物体検出」を体験します。
![ゴールイメージ](./img/220.png)

# 環境準備
あらかじめ [enebular](https://www.enebular.com/ja/) へサインアップして、フローエディターを起動しておいてください。

画面右上の「三(ハンバーガー)」メニューをクリックし、**パレットの管理** メニューを選択します。
![ハンバーガメニュー](./img/005.png)

「ユーザ設定画面」にて以下の操作をします。
## node-red-contrib-model-asset-exchange 追加
「画像の概要」や「物体検出」を処理するノードを追加します。

1. 左側メニューから **パレット** を選択します
2. 上部 **ノードを追加** タブを選択します
3. *ノードを検索* と表示されているテキストエリアに `model-asset` と入力します
4. 表示された **node-red-contrib-model-asset-exchange** の **ノードを追加** ボタンをクリックします

![node-red-contrib-model-asset-exchange追加](./img/020.png)

画面上部に警告ダイアログが表示されるので **追加** ボタンをクリックします。
![警告ダイアログ](./img/025.png)

## node-red-contrib-browser-utils 追加
ブラウザで 「ファイルのアップロード」や「webカメラ」を利用できるノードを追加します。
[前項](#node-red-contrib-model-asset-exchange-追加)に続いて「ユーザ設定画面」で操作します

1. *ノードを検索* と表示されているテキストエリアに `browser-util` と入力します
2. 表示された **node-red-contrib-browser-utils** の **ノードを追加** ボタンをクリックします
![browser-Util追加](./img/040.png)

## node-red-contrib-image-output 追加
フローエディタ上でイメージデータの確認することができるノードを追加します。
[前項](#node-red-contrib-model-asset-exchange-追加)に続いて「ユーザ設定画面」で操作します

1. *ノードを検索* と表示されているテキストエリアに `image-output` と入力します
2. 表示された **node-red-contrib-image-output** の **ノードを追加** ボタンをクリックします
![browser-Util追加](./img/200.png)

# image-caption-generator (画像概要取得) を試す
## サンプル読み込みとデプロイ
画面右上の「三(ハンバーガー)」メニューをクリックし、**読み込み** メニューを選択します。
![読み込み](./img/050.png)

「フローをクリップボードから読み込み画面」にて以下の操作をします。

1. 左側メニュから **サンプル** を選択します
2. 画面真ん中の「サンプル」ツリーを展開し、**image-caption-generator** を選択します
3. 画面下部の「読み込み」ボタンをクリックします。

![image-caption-generatorサンプル読み込み](./img/060.png)

そうすると、下記のフローが展開されます。
![image-caption-generatorフロー](./img/072.png)

画面右上の **デプロイ** ボタンをクリックし、フローをデプロイします。

![デプロイ](./img/053.png)

## サンプルの実行
あらかじめ、enebular 右側の「デバッグウインドウ」を有効にしておきます。
![デバッグウインドウ](./img/070.png)

展開されたフローの **file inject** ノードの左ボタンをクリックし、画像概要取得したいイメージファイルを選択します。
![ファイル選択](./img/100.png)

正しく処理されると「デバッグウインドウ」に「画像の概要メッセージ」が英語で表示されます。（下記例では、`a man in a suit and tie holding a toothbrush .` と判定されています）
![画像概要結果](./img/120.png)

# object-detector (物体認識) を試す
## サンプル読み込みとデプロイ
画面右上の「三(ハンバーガー)」メニューをクリックし、**読み込み** メニューを選択します。
![読み込み](./img/050.png)

「フローをクリップボードから読み込み画面」にて以下の操作をします。

1. 左側メニュから **サンプル** を選択します
2. 画面真ん中の「サンプル」ツリーを展開し、**object-detector** を選択します
3. 画面下部の「読み込み」ボタンをクリックします。

![object-detectorサンプル読み込み](./img/210.png)

そうすると、下記のフローが展開されますので、**デプロイ** しておいてください。
![object-detectorフロー](./img/105.png)

## サンプルの実行
展開されたフローの **file inject** ノードの左ボタンをクリックし、物体認識取得したいイメージファイルを選択します。
![ファイル選択](./img/215.png)

正しく処理されると、フロー下部の **image preview** ノードの下に、検出されたオブジェクトが視覚的に分かるように表示されます。まデバッグウインドウに、検出されたオブジェクトが出力されます。

![ファイル選択](./img/220.png)


# まとめ
Node-Red を使って画像認識を簡単に試すことができるということがわかりました。今回は既存のファイルを選択して試しましたが、カメラで撮影した画像をそのまま処理することもできるので、PCやスマホのWebカメラや、TJBot のカメラ入力から物体検出して…という何かが作成できる手ごたえを得ました。
