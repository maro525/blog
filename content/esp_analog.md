Title:ESP-WROOM-02をTOUTピンを使ってアナログ入力する
Date: 2017-08-01
Slug: esp_analog
Category: 電子工作
Tags: ESP-WROOM-02, Arduino

### 設定
* ツールメニューを開いた部分
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

### アナログ入力するには
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

### 光センサーについて

#### CdSセル
- CdSは「フォトレジスタ」という光センサの一種
- 光が上がると抵抗値が下がる
- CdS = 「硫化カドミウム(Cadmium sulfide)」
- 街灯を自動的にオン・オフさせるスイッチとしてよく使われている
- 明抵抗:10~20kΩ, 暗抵抗:1MΩ

### 回路
* 10kΩの抵抗をCdSセルのところにはさむ
* 分圧回路を2.2kΩと1.0kΩの抵抗で作る

#### 回路図
![No Image!]({filename}/image/ESP_Photocell.png)

### プログラム

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

### 参考記事
* [ハンダ付け不要！初心者でもできる、30分おきにセンサーの値をWeb上に保存する仕組みを安定して動かす（回路編） | Milkcocoa Engineers' Blog](http://blog.mlkcca.com/iot/save-sensor-values-by-half-hour/)
    - そもそもアナログ入ロクとは何かについて説明している
* [光に反応する回路－光センサ回路](http://startelc.com/elc/Works/elc_W_CdsTr.html)
* [ESP-WROOM-02 Arduino互換ボードで光センサーを使う - Qiita](http://qiita.com/umi_kappa/items/9a48208a45b78efc2fc1)
* [ESP-WROOM-02でアナログ入力 (TOUT/A0) [抵抗分圧回路]](http://www.petitmonte.com/robot/esp_wroom_02_analogread.html)
    - 抵抗分圧回路について
* [ちょと裏技っぽいけど、ESP8266 でAD変換やる方法 - Qiita](http://qiita.com/azusa9/items/26e74e4e0d5773ce9c41)
* [ESP8266(ESP-WROOM-02)でアナログ値を見るには？ - Qiita](http://qiita.com/ryokai/items/9b0c56616b4550eb05f1)
* [ESP8266 (ESP-WROOM-02) でセンサーを扱う - Qiita](http://qiita.com/exabugs/items/9ca4093813db71278b39)
* [Arduinoを30分でI2C通信する - Qiita](http://qiita.com/MergeCells/items/20c3c1a0adfb222a19cd)
