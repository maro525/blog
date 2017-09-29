Title: ESP-WROOM-02のWiFiまわりについて、いろいろ調べてみる
Date: 2017-09-28
Slug: esp_wifi
Category: 電子工作
Tags: ESP-WROOM-02

ESP-WROOM-02を使っていろいろ行ってきたが、WiFiと接続するときのtipsについてよくわかっていなかったので、調べてまとめてみる

### WiFiのモード

|モード|内容|接続|    
|:---|:---|:---|
|WIFI_STA|ステーションモード(無線LAN子機)|インターネットに接続可能|
|WIFI_AP|アクセスポイントモード(無線LAN親機)|この親機、子機はインターネットに接続不可|
|WIFI_AP_STA|WIFI_STAとWIFI_APの混合モード|親機はインターネット接続可、子機は不可|
|WIFI_OFF|WIFI機能をオフにする|   ｜
