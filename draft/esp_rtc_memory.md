Title: ESP-WROOM-02のRTCメモリーを使う
Date: 2017-09-29
Slug: esp_rtc_memory
Category: 電子工作
Tags: ESP-WROOM-02

ESP-WROOM-02を使ってなんらかのセンサーの値を取得する.

そして、電池節約のためにDeep-Sleepモードを使って一定時間ごとにWiFiに接続して情報をどこかへ送るとする.

さらに、ここからもっと節約のために、センサーの状態が大きく変わったときだけWiFiに接続して情報を送りたい.

こんなときに役立つのが、ESP-WROOM-02に内蔵されているRTCメモリである.

これは、Deep-Sleepモードに移行しても動き続けるメモリで、ユーザーが使える領域は512バイトある.

(※[このサイト](https://lowreal.net/2016/01/10/1)によると最初の4バイトは使えなかったので、正確には508バイトのようだ)

また、これは電源につないでいる状態であればずっと保持されるので、電池を基盤につないだ状態であれば情報は保持される.

一旦電池を抜いて、再び電池をつなぐと情報は初期化される.

それでは、コードを書いてみる.

[このページ](https://jiwashin.blogspot.jp/2016/07/esp30.html)を参考に実装してみる.

    