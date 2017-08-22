Title: ESP-WROOM-02のスリープモードまわり
Date: 2017-08-22
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

### 1分毎にdeep-sleepから復帰して光センサーの値を読み、IFTTTにセンサーの値を送ってみる

回路図は、[この記事](https://maro525.github.io/blog/esp_battery.html)にのっているのに追加して、RSTピンとIO１６ピンをつなげただけ.

```
#include <ESP8266WiFi.h> // WiFiモジュールのライブラリ
#include <Arduino.h>     // LEDを光らせるため

// SPI通信するためのライブラリ
extern "C"{
#include <spi.h>
#include <spi_register.h>
}

// wifiのSSID
const char* ssid = "nsketch";
// wifiのパスワード
const char* password = "nsketchm1";

// iftttの設定
const char* host = "maker.ifttt.com";
const char* event = "esp-wroom-02";
const char* secretkey = "rKt5tM6D_zuHd4sFRh07v";

// センサーの値を入れる変数
int val0, val1;

// 起床間隔
const unsigned long WAKEUP_INTERVAL = 60 * 1000 * 1000; // 1分

// LEDのピン
int led_pin = 5;

void setup() {

  // ステーションモードに設定
  WiFi.mode(WIFI_STA);
  // LightSleepモードに設定
  WiFi.setSleepMode(WIFI_LIGHT_SLEEP);

  // LEDの設定
  pinMode(led_pin, OUTPUT);

  // SPI通信のセットアップ
  spi_init(HSPI);

  // 起動のメッセージ
  Serial.begin(115200);
  Serial.println("hello this is esp-wroom-02");

  // LED光らせる
  digitalWrite(led_pin, HIGH);

  // 光センサーの値取得
  val0 = check(0);
  val1 = check(1);

  // シリアルモニターにセンサーの値を表示する
  Serial.print(" Sencor1:");
  Serial.print(val0);
  Serial.print(" Sencor2:");
  Serial.print(val1);
  Serial.print("\n");

  // IFTTTにアップロードする
  uploadIFTTT();

  // LEDを消す
  digitalWrite(led_pin, LOW);

  // ディープスリープに入る
  Serial.println("good night............");
  ESP.deepSleep(WAKEUP_INTERVAL);
}

void loop() {

}

// SPI通信でセンサーのADコンバータに入力されているアナログピンの値を読む関数
uint32 check(int channel) {
  uint8 cmd = (0b11 << 3) | channel;

  const uint32 COMMAND_LENGTH = 5;
  const uint32 RESPONSE_LENGTH = 12;

  uint32 retval = spi_transaction(HSPI, 0, 0, 0, 0, COMMAND_LENGTH, cmd, RESPONSE_LENGTH, 0);

  retval = retval & 0x3FF;
  return retval;
}


// IFTTTにアップロードする関数
void uploadIFTTT() {

  Serial.print("connecting to ");
  Serial.println(host);

  // WiFi接続
  WiFiClient client;
  const int httpPort = 80;
  if (!client.connect(host, httpPort)) {
      Serial.println("connection failed");
    }

  // IFTTTに送るURL
  String url = "trigger/";
  url += event;
  url += "/with/key/";
  url += secretkey;
  url += "?value1=";
  url += val0;
  url += "&value2=";
  url += val1;


  // サーバーに送る
  char sendData[256] = "";
  sprintf(sendData, "GET http://maker.ifttt.com/%s HTTP/1.1\r\nHost:maker.ifttt.com\r\nConnection: close\r\n\r\n", url.c_str());
  client.printf(sendData);

  int timeout = millis() + 5000;
  while (client.available() == 0) {
      if (timeout - millis() < 0) {
          Serial.println(">>> Client Timeout !");
          client.stop();
          return;
      }
  }
  delay(10);

  while(client.available()){
      String line = client.readStringUntil('\r');
      Serial.print(line);
  }
  delay(10);

  Serial.println();
  Serial.println("closing connection");
}


```

**無事にSlackに投稿されている様子を確認できた**

![No Image!]({filename}/image/deepsleep_slack.png)

### Light-Sleepについて調べてみる

[この記事](https://community.blynk.cc/t/esp8266-ligh　t-sleep/13584)によると、

```
WiFi.mode(WIFI_STA);
wifi_set_sleep_type(LIGHT_SLEEP_T);
```

のようにコードを書くだけで、delay関数の間、自動的にLight-Sleepモードい移行してくれるらしい.

しかし、今回は、センサーの値が一定のときはLight-sleepをしておいて、センサーの値が大きく変化したときにWi-Fiに接続する方法を用いたいので、この方法は使えない.

### 今後できるようになりたいこと

時間を設定して復帰するのではなく、光センサの値が変化したときにだけ、それがトリガーになってESP-WROOM-02がdeep-sleepモードから起動するような回路をくみたい.

[この記事](http://www.monoxit.com/%E8%AC%9B%E5%BA%A7/irmotion/)では、人感センサのHIGH,LOWを（回路的に)用いて、deep-sleepモードからの復帰をしている.

光センサの値によってdeep-sleepモードから復帰することは可能なのか.

[このような記事](光センサ回路（デジタルＩＣを使って）)のように、回路をうまく組めばいけそう.

だが、これをやるには回路の知識がないと時間だけがかかりそう.

### 参考記事
* [ESP8266 IoT] 人感センサーでESP8266の目をさまさす | MONOxIT](http://www.monoxit.com/%E8%AC%9B%E5%BA%A7/irmotion/)
* [電子工作と治具ツールの備忘録: ESP-WROOM-02のメモ(11)：3つのSleepの使い分け](http://ekjigtool.blogspot.jp/2015/08/esp-wroom-02113sleep.html)
* [電子工作と治具ツールの備忘録: ESP-WROOM-02プログラミング：スリープしながら定期的にツイート](http://ekjigtool.blogspot.jp/2015/08/esp-wroom-02_27.html)
* [ESP-WROOM-02のLIGHT-SLEEPとGPIOでの復帰 | 東京お気楽カメラ](http://okiraku-camera.tokyo/blog/?p=4996)
* [ESPr Developer + Ambientで自宅の環境モニターを作る – Ambient](https://ambidata.io/examples/weatherstation-1/)
* [ESP8266 (ESP-WROOM-02) の Deep Sleep でデータを引き継ぐ | tech - 氾濫原](https://lowreal.net/2016/01/10/1)
* [じわじわ進む: ESP単体で30分のデータをまとめて送信](https://jiwashin.blogspot.jp/2016/07/esp30.html)
* [ESP8266の真骨頂Deep-Sleepモードの使い方 - Qiita](http://qiita.com/azusa9/items/65a5c3772c41631b5ca1)
* [なんでも作っちゃう、かも。](http://arms22.blog91.fc2.com/?tag=esp8266)
* [ESP8266 Light Sleep - Projects made with Blynk - Blynk](https://community.blynk.cc/t/esp8266-light-sleep/13584^)
