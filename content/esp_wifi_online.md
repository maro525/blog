Title: ESP-WROOM-02をオンラインで設定する
Date: 2017-07-21
Slug: esp_wifi_online
Category: 電子工作
Tags: ESP-WROOM-02

# WiFiManager
* WiFiManagerを使うと、プログラムに書く必要なしに、WiFiに接続することができる
* [Githubのページ](https://github.com/tzapu/WiFiManager)からzipファイルをダウンロードして、Arduinoのライブラリに追加する
* [Sketch] ⇢ [Include Library] ⇢ [Manage Library]に行き、wifimanagerと検索すると出て来るので、インストール

# 使い方
* exampleの中にソースがいろいろあるので試すことができる.
* 基本的な使い方として、

```
#include <DNSServer.h>
#include <WiFiManager.h>
```

のようにライブラリをインクルードし、

```
WiFiManager wifiManager;
wifiManager.autoConnect("AutoConnectAP");
```
とプログラムにsetup()関数の中に書き込めばOK.

* その後、iPhoneか何かしらの機器で、AutoConnectAPに接続すると、画面が出てくるので、Configure WiFiの画面へ行き、手動でWiFiを設定する. ちなみにページのアドレスはhttp://192.168.4.1 であるが、wifiに接続すると勝手にページが表示される


* また、今までにすでに接続していれば、新しく接続する必要はない.

* 一旦接続したあとも、AutoConnectAPに接続すると、設定し直しなどすることができる

# 	参考
* [ESP8266 Wi-Fi SSID、パスワードのオンライン設定 - IoTラボ](http://takehikoshimojima.tumblr.com/post/138820924644/esp8266-wi-fi-ssid%E3%83%91%E3%82%B9%E3%83%AF%E3%83%BC%E3%83%89%E3%81%AE%E3%82%AA%E3%83%B3%E3%83%A9%E3%82%A4%E3%83%B3%E8%A8%AD%E5%AE%9A)
* [ESP8266 がアクセスポイントとなってSSID・パスワードの初期設定をする - Qiita](http://qiita.com/hotchpotch/items/eec0260b8b1938dda696)
    - SSIDやパスワードをどこに保存しているかに関する記事あり
