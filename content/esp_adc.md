Title: ESP-WROOM-02にADコンバーターを繋いで、複数のセンサの値を取得
Date: 2017-08-16
Slug: esp_adc
Category: 電子工作
Tags: ESP-WROOM-02

### ESP-WROOM-02
* ESP8266にはデフォルトでADコンバーターが付いている
* センサーが１つであれば、分圧回路を組み、TOUT端子を使用してデータを取得する
* TOUTピンを使用しない場合は、外部のADコンバーターを使用する
* ADコンバーターはSPIプロトコルで通信する. 使用するドライバは[Github](https://github.com/MetalPhreak/ESP8266_SPI_Driver)からダウンロード

### ADコンバーター
* 外付けADコンバーターは、MCP3002/3004/3008などある
* 精度は10bit(0-1023までの整数を返す).
* MCP3002とMCP3004/3008はプロトコルが異なる（？）
* MCP3002が2チャンネル、MCP3004が4チャンネル、MCP3008が8チャンネル
* MCP3002を使う場合、io15-抵抗(10kΩ)-GNDのように、pulldown回路を作る必要がある

### 回路
* io14 → clk(13)
* io12 → dout(12)
* io13 → din(11)
* io15 → cs(10)

![No Image!]({filename}/image/ESP_Photocell_ADConverter_bb.png)

#### SPI通信
「Serial Peripheral Interface」の略. 比較的近距離に配置されているデバイス同士をつなげるバスのこと

### 信号の機能
* cs = チップセレクト
* sck(clk) = クロック
* MOSI(din) = データ入力
* MOSO(dout) = データ出力

### プログラム
```
extern "C"{
#include <spi.h>
#include <spi_register.h>
}

// loopを回しやすくするライブラリ
#include <Ticker.h>

Ticker ticker;

void setup() {
  Serial.begin(115200);
  Serial.print("\n");

  spi_init(HSPI);

  ticker.attach_ms(1000, timer);
}

void loop() {
}

void timer() {

  uint32 val0 = check(0);
  uint32 val1 = check(1);

  Serial.print(" Sencor1:");
  Serial.print(val0);
  Serial.print(" Sencor2:");
  Serial.print(val1);
  Serial.print("\n");

}

uint32 check(int channel) {
  uint8 cmd = (0b11 << 3) | channel;

  const uint32 COMMAND_LENGTH = 5;
  const uint32 RESPONSE_LENGTH = 12;

  uint32 retval = spi_transaction(HSPI, 0, 0, 0, 0, COMMAND_LENGTH, cmd, RESPONSE_LENGTH, 0);

  retval = retval & 0x3FF;
  return retval;
}

```

### 結果
シリアルモニタで２つのセンサの値を確認することができた
![No Image!]({filename}/image/esp_adc_output.png)

### これから調べること
* ESP-WROOM-02のENピンの意味

### 参考記事
* [MCP3004/3008データシート](http://ww1.microchip.com/downloads/en/DeviceDoc/21295d.pdf)
* [ESP8266(ESP-WROOM-02)でアナログ値を見るには？ - Qiita](http://qiita.com/ryokai/items/9b0c56616b4550eb05f1)
* [ESP8266 (ESP-WROOM-02) でセンサーを扱う - Qiita](http://qiita.com/exabugs/items/9ca4093813db71278b39)
    - 回路を参考に
* [SPI通信で、PICと加速度センサを「お話」させてみた | 技ラボ](http://wazalabo.com/pic-spi.html)
    - SPI通信について
