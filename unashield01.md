# 1.これは何
sigfox デバイスを開発する際のプロトタイプモジュールとして使える arduino シールド
[製品ページ](https://soracom.jp/products/sigfox/sigfox_shield_for_arduino/)

# 2.sigfox UnaShield
## Sigfoxとは
```
IoT（モノのインターネット）の活用が広がる中、新たな無線通信技術として、
LPWA（Low Power Wide Area）が注目を集めています。
Sigfoxは、このLPWAのひとつで、低価格・低消費電力・長距離伝送を特長とした、
グローバルIoTネットワークです。
```
・世界中に展開している無線技術
・1国1事業者と契約をし、その事業者（SIGFOXオペレータ：SO）が国内のネットワーク構築運用を行うビジネスモデル

日本事業者は [京セラ](https://www.kccs.co.jp/sigfox/)

[sigfox 概要、使い方、海外での使用事例など](https://www.slideshare.net/hibigaku/sigfoxiot)

## シールドとは
arduino の上に積み上げるように接続する拡張基板

## arduino とは
Arduino（アルデュイーノ もしくは アルドゥイーノ）とは、AVRマイコン、入出力ポートを備えた基板、C言語風のArduino言語とそれの統合開発環境から構成されるシステム。Arduino LLC および Arduin SRL が設計・製造を行い、登録商標を持っている。

[arduino(wikipedia)](https://ja.wikipedia.org/wiki/Arduino)

# 3.使ってみる
## デバイス側の準備 〜 プログラム準備　〜　データ送信まで
[取り扱い説明書](https://www.kccs.co.jp/sigfox/download/manual-sigfox-shield-for-arduino.pdf)

取説通りで大丈夫なはず。
取説に記載されているサンプルプログラムが送信するデータは、意味のない値を送信するだけになっているので
少し手を加えたほうが、触っていて楽しいかも知れないです

**（取説 [4. 12バイト（16進表記）の送信時は、transceiver.sendMessageを使用します。] の部分...**
#### 変更前
```
  transceiver.sendMessage(“0102030405060708090a0b0c”); // 最大12バイトデータの送信.
  delay(10000); // 10秒間待つ.
```
#### 変更後
```
  transceiver.sendString("Yuka=Nakano-");
//transceiver.sendMessage(“0102030405060708090a0b0c”); // 最大12バイトデータの送信.
  delay(10000); // 10秒間待つ.
```


## プログラマが sigfox のデータ(Message)にアクセスするまでの手順
### sigfox ポータルで確認
#### 1.sigfox portal にログイン
![backend にログイン](https://dl.dropboxusercontent.com/s/5sh8suu17nce7cu/sigfox-00-01.png)

* * *

#### 2.画面上部メニューから「Device」をクリック
![Device](https://dl.dropboxusercontent.com/s/go1zgrqmaqjt8x2/sigfox-00-02.png)
画面下部の一覧から、表示したいデータの送信元デバイスを見つけ、[id] をクリック

* * *

#### 3.デバイス詳細ページの左メニューから「Message」をクリック
![Device](https://dl.dropboxusercontent.com/s/l63kcrxnw4j1f28/sigfox-00-03.png)

* * *

#### 4.送信時間、送信時の電波強度とともに、デバイスが送信したデータが一覧表示される
![Device](https://dl.dropboxusercontent.com/s/96lxvo4y82mj2pe/sigfox-00-04.png)
一覧上の ◇ マークをクリックすると、そのデータを送信したデバイスの大まかな電波範囲が表示されます。
文字列で送信したデータも、[ascii コードの１６進数](http://e-words.jp/p/r-ascii.html)で表示されます。

```
59756b613d4e616b616e6f21
```
↓
```
59 75 6b 61 3d 4e 61 6b 61 6e 6f 21
```
↓
```
Y u k a = N a k a n o !
```
* * *

[開発者用ドキュメントページ(ハードウェア側の参考)](https://unabiz.github.io/unashield/hardware.html)
配線さえわかれば、arduino がなくても、RaspberryPi や、AVR マイコン、パソコンからのシリアル通信、その他電子回路から sigfox の通信を試せます。
