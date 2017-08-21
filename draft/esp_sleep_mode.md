Title: ESP-WROOM-02のスリープモードまわり
Date: 2017-08-21
Slug: esp_sleep_mode
Category: 電子工作
Tags: ESP-WROOM-02

### esp8266のSleepモードについて

**sleepモードは3種類ある**

* deep-sleep
		- 記事
				* [ESP8266の真骨頂Deep-Sleepモードの使い方 - Qiita](http://qiita.com/azusa9/items/65a5c3772c41631b5ca1)
				* [ESP8266 (ESP-WROOM-02) で消費電力を抑えるには (スリープモードまとめ) - Qiita](http://qiita.com/exabugs/items/9edf9e2ba8f69800e4c5)
		- sleepモードのうち最も消費電力が少ない
		- 復旧時、再起動されて、setup()から実行される
		- IO16ピンをRST（リセット）に繋ぐ必要がある
		- 一定間隔で何かを実行するのに向いている
		- マイコン部分まですべて電源が切れるので、変数を所持したい場合などは、工夫が必要
		- RTCメモリーというメモリーが動き続けているので、それに変数等データを書き込むことができる
		- RTCメモリーはユーザーエリアが512バイトある.
		- 先人によると、最初の4バイトはなぜか読み込めないので、正確には508バイトしか使えないらしい

* light-sleep
		- deep-sleepの次に消費電力が少ない
		- WIFI_STA mode(ステーションモード）のみで実行可能
		- 通信が行われないときにはWiFi用のRF回路をオフにし、CPの実行をサスペンドする
		- WiFiとの接続は維持されたまま
		- 0.9mA消費


* modem-sleep
		- sleepモードの中で最も消費電力が大きい
		- CPUの動作を維持したまま、非通信時にWiFi用のRF回路をオフにして電力をおさえる
		- WiFiとの接続は維持されたまま
		- 15mA消費

### 光センサーの値で、ESP-WROOM-02をスリープモードから復帰させてみる

[この記事](http://www.monoxit.com/%E8%AC%9B%E5%BA%A7/irmotion/)では、人感センサのHIGH,LOWを（回路的に)用いて、deep-sleepモードからの復帰をしている.

光センサの値によってdeep-sleepモードから復帰することは可能なのか.

[このような記事](光センサ回路（デジタルＩＣを使って）)のように、回路をうまく組めばいけそう.

### 参考記事
* [ESP8266 IoT] 人感センサーでESP8266の目をさまさす | MONOxIT](http://www.monoxit.com/%E8%AC%9B%E5%BA%A7/irmotion/)
* [電子工作と治具ツールの備忘録: ESP-WROOM-02のメモ(11)：3つのSleepの使い分け](http://ekjigtool.blogspot.jp/2015/08/esp-wroom-02113sleep.html)
* [電子工作と治具ツールの備忘録: ESP-WROOM-02プログラミング：スリープしながら定期的にツイート](http://ekjigtool.blogspot.jp/2015/08/esp-wroom-02_27.html)
* [ESP-WROOM-02のLIGHT-SLEEPとGPIOでの復帰 | 東京お気楽カメラ](http://okiraku-camera.tokyo/blog/?p=4996)
* [ESPr Developer + Ambientで自宅の環境モニターを作る – Ambient](https://ambidata.io/examples/weatherstation-1/)
* [ESP8266 (ESP-WROOM-02) の Deep Sleep でデータを引き継ぐ | tech - 氾濫原](https://lowreal.net/2016/01/10/1)
* [じわじわ進む: ESP単体で30分のデータをまとめて送信](https://jiwashin.blogspot.jp/2016/07/esp30.html)
* [ESP8266の真骨頂Deep-Sleepモードの使い方 - Qiita](http://qiita.com/azusa9/items/65a5c3772c41631b5ca1)
