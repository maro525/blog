Title: ArduinoからIFTTTを使ってslackに投稿する
Date: 2017-07-25
Slug: esp_ifttt
Category: 電子工作
Tags: ESP-WROOM-02, Arduino, IFTTT

### IFTTTで設定
- search欄からWebhooksと検索する
- Connectする
- httpsでGETコマンドでイベントとキーを送れる
- iftttのレシピを作る
- 通知先をslackに指定
- Search欄からWebhooksを検索して、Documentationを見ると、シークレットキーや、使い方の説明が見れる


### Arduinoに書き込むプログラム
```
const char* host = "maker.ifttt.com";
const char* event = "IFTTTに設定したイベント名";
const char* secretkey = "********";
```
のように変数を設定

```
WiFiClient client;
const int httpPort = 80;
if (!client.connect(host, httpPort)) {
	Serial.println("connection failed");
	return;
}
```
↑IFTTTに接続

```
String url = "/trigger/";
url += event;
url += "/with/key/";
url += secretkey;
url += "?value1=";
url += value1;
url += "&value2=";
url += value2;
url += "&value3=";
url += value3;

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

```
↑IFTTに送信


### 参考記事
* [ESP-WROOM-02で取得したセンサーの値をIFTTTに渡す - Qiita](http://qiita.com/snumano/items/ad453fd6137249c40147)
* [IFTTTにMaker Channelができました | スイッチサイエンス マガジン](http://mag.switch-science.com/2015/06/25/ifttt-maker-channel/)
