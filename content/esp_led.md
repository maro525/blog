Title: ArduinoでWiFiモジュールを使って、Lチカまでやってみる
Date: 2017-07-16
Slug: esp_led
Category: 電子工作
Tags: ESP-WROOM-02, Arduino

#### パーツ
* ESPr Developer(ESP-WROOM-02開発ボード )[https://www.switch-science.com/catalog/2500/](https://www.switch-science.com/catalog/2500/)

#### 手順

##### 1.ArduinoにESP-WROOM-02ボードを追加する
* ファイル」-「環境設定」を開く
* [https://github.com/esp8266/Arduino](https://github.com/esp8266/Arduino) のInstalling with Boards ManagerのところにあるURLをコピーし、Additional Boards Managerに貼り付ける. (複数のURLがある場合はコンマでつなげる)
* 入力したらOKを押して閉じる. 次に「Tools」から「Boards」- 「Boards Maneger」を選択して開く
* 上にある検索欄に「ESP82」と入力すると、「esp8266」が出てくるので、インストール
* インストールが完了したらArduinoを再起動する。「Tools」-「Board」を開くと、ESP8266に関係するボード一式が追加されている

##### 2.開発ボードをピンではんだ付けする


##### 3.プログラムを動かす
* Bordの中から「Generic ESP8266 Module」を選択する
* このとき、ESP8266ボード設定のReset Methodを"nodemcu"にする必要がある. "nodemcu"では、自動で書き込みモードに切り替えて書き込みができるが、"ck”の場合はGPIO #0 を押しながらリセットキーを押さないと書き込み可能にならない.

#### Lチカやってみる


```
 void setup() {
  pinMode(13, OUTPUT);

}

void loop() {
  digitalWrite(13, HIGH);
  delay(1000);
  digitalWrite(13, LOW);
  delay(1000);
}
```
