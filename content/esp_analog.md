Title:ESP-WROOM-02をTOUTピンを使ってアナログ入力する
Date: 2017-08-01
Slug: esp_analog
Category: 電子工作
Tags: ESP-WROOM-02, Arduino

#### 設定
- ツールメニューを開いた部分
	- ボード : Generic ESP8266 Module
	- Flash Mode : QIO
	- Flash Frequency : 80MHz
	- CPU Frequency : 160MHz
	- Flash Size : 4M(1M SPIFFS) or (3M spiffs)
	- Debug port : "Disabled"
	- Debug Level : "なし”
	- Reset Method : nodemcu
	- Upload Speed : 921600
	- 書き込み装置 : USBasp

#### アナログ入力するには
- Arduino UNOでは、アナログ入力ピンがA0~A4とあるが、ESP-WROOM-02には存在しない. デジタルピンも、1, 3~4, 6~11がない.
- ESP-WROOM-02にはanalogRead関数が用意されていない.
- ESP-WROOM-02でアナログ入力を行うには、TOUTピンで「system_adc_read()」という関数を使って、アナログ値を取得して行う
- system_adc_read()を使うには

```
extern "C" {
#include "user_interface.h"
}
```

- しかし、TOUTピンは、通常のArduinoのアナログピンとは違い、0v~1vの入力値の検出はできるが、3.3vの値をそのままTOUTピンに入力するとボードが壊れてしまう可能性がある. そのため、分圧回路を組む必要がある.

#### 光センサーについて

##### CdSセル
- CdSは「フォトレジスタ」という光センサの一種
- 光が上がると抵抗値が下がる
- CdS = 「硫化カドミウム(Cadmium sulfide)」
- 街灯を自動的にオン・オフさせるスイッチとしてよく使われている
- 明抵抗:10~20kΩ, 暗抵抗:1MΩ

#### 回路
* 10kΩの抵抗をCdSセルのところにはさむ
* 分圧回路を2.2kΩと1.0kΩの抵抗で作る

##### 回路図
![No Image!]({filename}/image/ESP_Photocell.png)

#### 光センサーの値を取得し、シリアルモニターで値を確認するプログラム
```
#include <Arduino.h>

extern "C" {
#include "user_interface.h"
}

int adc_key_in = 0;

void setup() {
  Serial.begin(115200);
}

void loop() {
  adc_key_in = system_adc_read();
  String value = String(adc_key_in);
  Serial.println(value);
}

```

#### 光センサーの値を取得し、webに送るプログラム
```
#include <ESP8266WiFi.h>
#include <Servo.h>
extern "C" {
#include "user_interface.h"
}

const char *ssid = "******";
const char *password = "******";

// Create an instance of the server
// specify the port to listen on as an argument
WiFiServer server(80);

int val = 0;

void setup() {
 Serial.begin(115200);
 delay(10);

 // Connect to WiFi network
 Serial.println();
 Serial.println();
 Serial.print("Connecting to ");
 Serial.println(ssid);

 WiFi.begin(ssid, password);

 while (WiFi.status() != WL_CONNECTED) {
 delay(500);
 Serial.print(".");
 }
 Serial.println("");
 Serial.println("WiFi connected");

 // Start the server
 server.begin();
 Serial.println("Server started");

 // Print the IP address
 Serial.println(WiFi.localIP());
}

void loop() {
  val = system_adc_read();
  Serial.println(string(val));

 delay(1);
}

void sendHttp() {
  // Check if a client has connected
  WiFiClient client = server.available();
  if (!client) {
  return;
  }

  // Wait until the client sends some data
  Serial.println("new client");
  while(!client.available()){
  delay(1);
  }

  // Read the first line of the request
  String req = client.readStringUntil('\r');
  Serial.println(req);
  client.flush();


  client.flush();

  // Prepare the response
  String s = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<!DOCTYPE HTML>\r\n<html>\r\n value is now ...  ";
  s += val;
  s += "</html>\n";

  // Send the response to the client
  client.print(s);
  Serial.println("Client disonnected");

  // The client will actually be disconnected
  // when the function returns and 'client' object is detroyed
}
```

ブラウザ上で光センサーの値を見ることができる


#### 光センサーの値で、暗くなったり明るくなったらそれを判別してwebに送るプログラム

```
#include <ESP8266WiFi.h>
#include <Servo.h>
extern "C" {
#include "user_interface.h"
}

const char *ssid = "*****";
const char *password = "*****";

// Create an instance of the server
// specify the port to listen on as an argument
WiFiServer server(80);

int val = 0;
int pre_val = 0;
int threshold = 30;
int mini_threshold = 10;
int brightcounter = 0;
int darkcounter = 0;
bool front = true;
bool checking = false;

void setup() {
 Serial.begin(115200);
 delay(10);

 // Connect to WiFi network
 Serial.println();
 Serial.println();
 Serial.print("Connecting to ");
 Serial.println(ssid);

 WiFi.begin(ssid, password);

 while (WiFi.status() != WL_CONNECTED) {
 delay(500);
 Serial.print(".");
 }
 Serial.println("");
 Serial.println("WiFi connected");

 // Start the server
 server.begin();
 Serial.println("Server started");

 // Print the IP address
 Serial.println(WiFi.localIP());
}

void loop() {
  val = system_adc_read();

    if(val <= 50 && pre_val <= 50){
      if(front){
        darkcounter += 1;
        Serial.println("darkcountercounting");
      }
    } else if (val >= 150 && pre_val >= 150) {
      if(!front){
        brightcounter += 1;
        Serial.println("brightcountercounting");
      }
    } else {
      darkcounter = 0;
      brightcounter = 0;
    }

    if(darkcounter == 5 || brightcounter == 5) {
      darkcounter = 0;
      brightcounter = 0;
      sendHttp();
      front = !front;
    }

  if(front) {
    Serial.println("frontfrontfrontfrontfront");
  } else {
    Serial.println("backbackbackbackbackback");
  }

 delay(1000);

 pre_val = val;
}

void sendHttp() {
  // Check if a client has connected
  WiFiClient client = server.available();
  if (!client) {
  return;
  }

  // Wait until the client sends some data
  Serial.println("new client");
  while(!client.available()){
  delay(1);
  }

  // Read the first line of the request
  String req = client.readStringUntil('\r');
  Serial.println(req);
  client.flush();


  client.flush();

  // Prepare the response
  String s = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<!DOCTYPE HTML>\r\n<html>\r\n value is now ...  ";
  s += val;
  s += "</html>\n";

  // Send the response to the client
  client.print(s);
  Serial.println("Client disonnected");

  // The client will actually be disconnected
  // when the function returns and 'client' object is detroyed
}

```

#### 参考記事
* [ハンダ付け不要！初心者でもできる、30分おきにセンサーの値をWeb上に保存する仕組みを安定して動かす（回路編） | Milkcocoa Engineers' Blog](http://blog.mlkcca.com/iot/save-sensor-values-by-half-hour/)
	* そもそもアナログ入ロクとは何かについて説明している
* [光に反応する回路－光センサ回路](http://startelc.com/elc/Works/elc_W_CdsTr.html)
* [ESP-WROOM-02 Arduino互換ボードで光センサーを使う - Qiita](http://qiita.com/umi_kappa/items/9a48208a45b78efc2fc1)
* [ESP-WROOM-02でアナログ入力 (TOUT/A0) [抵抗分圧回路]](http://www.petitmonte.com/robot/esp_wroom_02_analogread.html)
	* 抵抗分圧回路について
* [ちょと裏技っぽいけど、ESP8266 でAD変換やる方法 - Qiita](http://qiita.com/azusa9/items/26e74e4e0d5773ce9c41)
* [ESP8266(ESP-WROOM-02)でアナログ値を見るには？ - Qiita](http://qiita.com/ryokai/items/9b0c56616b4550eb05f1)
* [ESP8266 (ESP-WROOM-02) でセンサーを扱う - Qiita](http://qiita.com/exabugs/items/9ca4093813db71278b39)
* [Arduinoを30分でI2C通信する - Qiita](http://qiita.com/MergeCells/items/20c3c1a0adfb222a19cd)
