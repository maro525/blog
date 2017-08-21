Title: ESP-WROOM-02のスリープモードまわり
Date: 2017-08-12
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

### 参考記事
* [Arduinoをリチウムイオン電池で動かすには | スイッチサイエンス マガジン](http://mag.switch-science.com/2016/02/12/arduino-lithium/)w
* [ESP-WROOM-02 で、モバイル物理スイッチを作る - Qiita](http://qiita.com/ie4/items/ae850cdb2c617f3fd6af)
		- 設計が参考になる
* [Hue Tapスイッチが高いのでESP-WROOM-02で物理スイッチを自作する　後編 | MUDAなことをしよう。](http://make-muda.weblike.jp/2016/07/4209/)
* [スイッチサイエンス「Arduinoをリチウムイオン電池で動かすには」](http://mag.switch-science.com/2016/02/12/arduino-lithium/)
		- 基本的な説明がのっている
		- リチウムイオン電池とピッチ変換機を使って基盤につなぐ
* [ESP-WROOM-02 で、モバイル物理スイッチを作る - Qiita](http://qiita.com/ie4/items/ae850cdb2c617f3fd6af)
		- こちらも回路が書いてある
* [ラジオペンチ 消費電流が激減！ delayWDT関数を作ってみた](http://radiopench.blog96.fc2.com/blog-entry-486.html)
		- Arduinoを省電力設計する
* [ESP-WROOM-02のLIGHT-SLEEPとGPIOでの復帰 | 東京お気楽カメラ](http://okiraku-camera.tokyo/blog/?p=4996)
		- コンデンサの製品の特性を比べたり、電池の特性を比べている
		- 回路設計にも参考になる
* [esp8266(esp-wroom-02)　電池ボックスから電源供給](http://knaka0209.blogspot.jp/2015/08/esp-wroom-02-05.html)
		- レギュレーター、コンデンサなどの回路の説明あり
* [ESPr Developer + Ambientで自宅の環境モニターを作る – Ambient](https://ambidata.io/examples/weatherstation-1/)
		- バッテリー＋分圧回路などの回路図が載っている
