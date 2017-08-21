Title: ESP8266, ESP-WROOM-02, ESPr Developerについてまとめ
Date: 2017-07-20
Slug: esp_info
Category: 電子工作
Tags: ESP-WROOM-02


### 通信について

**通信には3種類ある。**

- Wi-Fi
- Bluetooth
- LTE通信

それぞれの強みがある。

* Wi-Fi : 家の中
* Bluetooth : 遅延が少ない状況での通信
* LTE : 外の環境

Arduinoで無線LANを利用する方法は3種類ある。

* ArduinoとWi−Fiモジュールが一体となっているArduino YÚN。9990円
* ArduinoとWi-Fiシールド。8432円
* ArduinoとWi-Fiモジュール。4104円。最も安価に無線LANを利用できる。

モジュールは、ESP8266という名前で、以前は、技適（電波を使うための総務省からの許可のようなもの）が未取得だったが、取得したことによって、名前を変えて新しく売られることになった。
それが、ESP-WROOM-02 Wi-Fiモジュール。

### ESP-WROOM-02 ブレークアウトボード
もとのWi-Fiモジュール単体では、ピンの間隔が1.5mmとなっているため、ブレッドボードの間隔と異なる.

そのためブレッドボードに載せるようなブレークアウトボードが発売されている.

* [Ｗｉ－Ｆｉモジュール　ＥＳＰ－ＷＲＯＯＭ－０２　ＤＩＰ化キット: 無線、高周波関連商品 秋月電子通商 電子部品 ネット通販](http://akizukidenshi.com/catalog/g/gK-09758/)
* [Amazon.co.jp： ESP-WROOM-02 搭載mikroBUS(R)対応ブレークアウトボード Ver.2: おもちゃ](https://www.amazon.co.jp/gp/product/B015X3P2H4/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=247&creative=1211&creativeASIN=B015X3P2H4&linkCode=as2&tag=deko0f-22)

### ESPr Developer
* ESP-WROOM-02の開発ボードで、ESP-WROOM-02に加えて、シリアル変換やレギュレータがセットになっている
* ESP8266は、上海のファブレス半導体メーカー Espressif Systemsが製造している
* ピンヘッダをはんだ付けして、ブレッドボードに接続する
* ESP-WROOM-02とUSBシリアル変換やレギュレータ等を使うより、ESPr Developerを使ったほうが、若干安い

この基盤には、

* ESP8266本体
* 3.3V対応USBシリアル変換ＩＣ　FT231XS
* 5V→3.3V 電源レギュレーター　XC6222B　（最大出力電流700mA）

が載っている

#### 仕様
* IEEE 802.11b/g/nに対応 (WiFi 2.4GHz)
* 低消費電力の32 bit MCUを搭載
* 4Mbyteのフラッシュメモリ
* 10 bitA/Dコンバータを搭載
* TCP/IPのプロトコル・スタックを搭載
* SDIO, (H)SPI, UART, I2C, I2S, IrDA, PWM, GPIOなどさまざまなインターフェースを搭載
* ディープスリープモード時10uA未満、電源オフ時5uA未満
* 起動からパケット送信まで2ms未満
* スタンバイ時消費電力 1mW未満（DTIM3）
* +20 dBm出力（802.11b使用時）
* -40℃〜125℃
* FCC、CE、工事設計認証取得済
* FT231XS搭載
* リセットスイッチ、動作モード切替スイッチ搭載
* 動作電圧：3.3 V
* 電源ピン
	 	- VINピン : USB以外から電源を取るときに使います。3.7～6.0 V
		- VOUTピン : 5Vピンの名称をVOUTピンに変更しました。
	 	- 3.3Vピンは出力専用ピン

### 動作環境
* Arduino IDE
* Lua
* Python -> [MicroPython](https://github.com/micropython/micropython/tree/master/esp8266)
* NodeJS

### モードについて
* ATコマンドでやりとりしたり書き込んだスケッチを実行する「Flash Boot Mode(実行モード）」とスケッチを書き込んだりファームアップデートを行うための「UART Download Mode(書き込みモード）」がある
* IO0ピンがHIGHのとき、Flash Boot Modeになる
* Arduinoスケッチをフラッシュに書き込むと、フラッシュメモリをすべて書き換えてArduino化することになり、ATコマンドは使えなくなる.
再びATコマンドに戻りたいときは、ATコマンド仕様のフラッシュに書き換える.

### ボタン
* リセットボタンを押すとリセットがかかる
* モード切り替えボタンはIO0番ピンにつながっている.ボタンを押すとIO0がLOW、放すとIO0がHIGHになる.
* モード切り替えボタンを押しながらリセットボタンを押すと、UART Download Modeになる

### 参考
* [ESPr® Developer（ESP-WROOM-02開発ボード） - スイッチサイエンス](https://www.switch-science.com/catalog/2500/)
* [ESP-WROOM-32 ( ESP32 ) で Wi-Fi スマホ LED スイッチ作り | mgo-tec電子工作](https://www.mgo-tec.com/blog-entry-esp-wroom-32-esp32-wifi-smartphone-led-switch.html)
		- 開発ボードを使わずに回路づくりをするときに参考になりそう
* [ESP-WROOM-02](http://ht-deko.com/arduino/esp-wroom-02.html)
 		- このシリーズの様々な基盤や回路の説明など詳細な情報が載っている
* [Check! IoT ができるようになるまで ～ まずはシリアル通信を使えるようになる - Qiita](http://qiita.com/dz_/items/277eba8cb760b81a2688)
* [ESP-WROOM-02使ってみる | Natural Days](https://www.hirotakaster.com/weblog/esp-wroom-02%E4%BD%BF%E3%81%A3%E3%81%A6%E3%81%BF%E3%82%8B/)
* [Wifiにつながるデバイス(ESP8266EX, ESP-WROOM-02, ESPr Developerなど)](https://www.slideshare.net/nishio/wifiesp8266ex-espwroom02-espr-developer)
* [ESP8266をはじめよう](https://www.slideshare.net/quitsq/esp8266-60164967)
* [GWだし、ESP8266関係の記事をまとめながら、IoT framework作ってみる - Qiita](http://qiita.com/hidenorly/items/0eadcf6cc8ba04c0145d#_reference-84f2d28391d764922e0d)

 		- ESP8266関係の参考になるリファレンスがたくさん載っているQiita記事
