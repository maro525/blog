Title: ESP-WROOM-02をバッテリーで動かす
Date: 2017-08-16
Slug: esp_battery
Category: 電子工作
Tags: ESP-WROOM-02

以前に作った、ADコンバーターを使って2つのセンサーの値を取得した回路を使って、バッテリー駆動してみる.

### バッテリーの種類
* 乾電池
  * NiMH電池 → 5.4~4.0V
  * アルカリ乾電池 → 4.95~2.7V
    * [ディープスリープモードで3ヶ月ほど持ち続けている](https://blogs.yahoo.co.jp/bokunimowakaru/55401333.html)
    * [特定時しか動作しないようにして約6.8ヶ月の動作を確認](https://blogs.yahoo.co.jp/bokunimowakaru/55477942.html)
    * [乾電池2個と昇圧電源回路で駆動](http://cl.hatenablog.com/entry/esp-wroom-02-tps63000)
* リチウムイオン電池
* 空気亜鉛電池
  * 小型で大容量
  * [ESP-WROOM-02には向いてないっぽい](https://blogs.yahoo.co.jp/bokunimowakaru/folder/1681421.html)

### 回路の組み方
##### 電圧

ESP-WROOM-02は、3.3Vをかけなければ動かない.

また、瞬間的に350mAぐらい(コンデンサーに使っては変わる？他のサイトでは200mA)使うので、それを流しても落ちない電圧が必要.

そして、Wi-Fi接続時は、75mA前後の電流消費で、時々120~180mA程度の電流が流れることがあるらしい.

##### 三端子レギュレーター

電圧には変動があるので、回路の中で電源として用いる場合は、出力電圧が一定な3.3V出力の三端子レギュレーターを使うことになる.

しかし、三端子レギュレーターの仕組みとして、入出力電圧の差に相当する損失（ドロップアウト）が生じる.

なので、3.3Vの安定した出力電圧を作るためには、入力電圧に損失分も加えた電圧をレギュレータに投入する必要がある.

秋月電子に、何種類か三端子レギュレーターが売っていて、今回は、Wi-Fiモジュールの特性を考えて、スタンバイ電流が少ないNJU7223F33を用いることにする.

##### コンデンサー
レギュレーターの入力側と出力側にコンデンサーを入れる

コンデンサーを付加しないと、3端子レギュレーターが発振することがある. （発振 = 回路に振動が発生してしまうこと)

入力側のコンデンサの容量は0.1~0.3μF程度のセラミックコンデンサ(周波数特性のよいもの)をつける

出力側には、出力電流とレギュレーターでドロップする電圧からレギュレーターでの熱消費を計算して、必要な場合は放熱器をつける.

出力側のコンデンサーの容量が47μFのとき、電源の立ち上がり時に200mAの電流が流れるらしい.

### 回路図
![No Image!]()

### 簡単なLチカをやってみる
バッテリーとつないでLEDのがつくことは確認できた.

しかし、バッテリー駆動だと、LEDの点灯（１秒ON↔1秒OFF)のプログラムの動作ができない. (ESP-WROOM-02の書き込みモードにできてない？)

### バッテリーでWiFI通信してみる
IFTTTに一定時間ごとにセンサーの光センサー２つの情報を送ってみる.

IFTTTとの連携についての[記事](https://maro525.github.io/blog/esp_ifttt.html)

プログラムは以下のような感じ
```
#include <ESP8266WiFi.h>
#include <Ticker.h>
#include <Arduino.h>

extern "C"{
#include <spi.h>
#include <spi_register.h>
#include "user_interface.h"
}

// wifiのSSID
const char* ssid = "nsketch";
// wifiのパスワード
const char* password = "nsketchm1";
// iftttの設定
const char* host = "maker.ifttt.com";
const char* event = "esp-wroom-02";
const char* secretkey = "rKt5tM6D_zuHd4sFRh07v";

Ticker ticker;

int led = 16;

uint32 val0, val1;

void setup() {
  Serial.begin(115200);
  Serial.print("Connecting to ");
  Serial.println(ssid);

  pinMode(led, OUTPUT);
  spi_init(HSPI);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected");

  // Print the IP address
  Serial.println(WiFi.localIP());

  ticker.attach_ms(1000, sendifttt);
}

void check() {
  val0 = check(0);
  val1 = check(1);
}

void sendifttt(){

  check();

  WiFiClient client;
  const int httpPort = 80;
  if (!client.connect(host, httpPort)) {
      Serial.println("connection failed");
      return;
  }

  // iftttに送るデータの文字列
  String url = "trigger/";
  url += event;
  url += "/with/key/";
  url += secretkey;
  url += "?value1=";
  url += val0;
  url += "?value2=";
  url += val1;

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

  // Read all the lines of the reply from server
  // and print them to Serial
  while(client.available()){
      String line = client.readStringUntil('\r');
      Serial.print(line);
  }
  delay(10);

  Serial.println();
  Serial.println("closing connection");
}

uint32 check(int channel) {
  uint8 cmd = (0b11 << 3) | channel;

  const uint32 COMMAND_LENGTH = 5;
  const uint32 RESPONSE_LENGTH = 12;

  uint32 retval = spi_transaction(HSPI, 0, 0, 0, 0, COMMAND_LENGTH, cmd, RESPONSE_LENGTH, 0);

  retval = retval & 0x3FF; // mask to 10-bit value
  return retval;
}

void loop() {
  check();

  // 1つめの光センサーの値の2つめのセンサーの値以上のときはLEDが点灯するようにする
  if(val0 >= val1) {
    digitalWrite(led, HIGH);
  } else {
    digitalWrite(led, LOW);
  }
  delay(1000);
  digitalWrite(led, LOW);
  delay(1000);
}

```

しかし、コネクションロストと出てしまい、動作できいない.

### これから調べること
* コンデンサ周りの計算方法
* バッテリー駆動をしたいときのプログラム書き込み方法

#### 参考記事
* [Arduinoをリチウムイオン電池で動かすには | スイッチサイエンス マガジン](http://mag.switch-science.com/2016/02/12/arduino-lithium/)
* [esp8266(esp-wroom-02)　電池ボックスから電源供給](http://knaka0209.blogspot.jp/2015/08/esp-wroom-02-05.html)
* [ESP-WROOM-02とBME280 電池駆動で温度／湿度／気圧測定 (その1) | 東京お気楽カメラ](http://okiraku-camera.tokyo/blog/?p=5032)
* [ESP8266の消費電流の徹底調査 – Ambient](https://ambidata.io/examples/esp8266-current/)
* [ESP8266+ESP32 - 詳細表示 - ボクにもわかる電子工作 - Yahoo!ブログ](https://blogs.yahoo.co.jp/bokunimowakaru/folder/1681421.html)
  * 空気亜鉛電池の説明
* [ESP-WROOM-02 と心拍センサーをバッテリーで動かしてみた。：息子と一緒に Makers：So-netブログ](http://makers-with-myson.blog.so-net.ne.jp/2016-06-02)
  * リチウムイオン電池の回路説明あり
* [ESP-WROOM-02を昇圧電源回路で乾電池2本（2.4V）駆動 - M.C.P.C. (Mamesibori Creation Plus Communication)](http://cl.hatenablog.com/entry/esp-wroom-02-tps63000)
* [Hue Tapスイッチが高いのでESP-WROOM-02で物理スイッチを自作する　後編 | MUDAなことをしよう。](http://make-muda.weblike.jp/2016/07/4209/)
* [ESP8266の消費電流の徹底調査 – Ambient](https://ambidata.io/examples/esp8266-current/)
  * ESP8266の消費電流について詳しい記述がある
  * 乾電池3つで駆動させた回路図参考になる
* [三端子レギュレータの使い方](http://www.riric.jp/electronics/design/power/regu3pin_usage.html)
* [LDOレギュレーターのはなし: SUDOTECK](http://sudoteck.way-nifty.com/blog/2010/06/ldo-b66b.html)
  * Low DropOutレギュレーターの話
* [34391_onboard.pdf](http://www.cqpub.co.jp/hanbai/books/34/34391/34391_onboard.pdf)
  * 三端子レギュレーターの基本動作と正しい使い方
  * 内部の仕組みの説明
