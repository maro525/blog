Title: Ubuntu14.04にUSBを接続する
Date: 2017-09-30
Slug: ros_setup
Category: Ubuntu
Tags: Ubuntu, ROS

### Extensionパックのダウンロード

[このページ](https://www.virtualbox.org/wiki/Downloads)から、Extension Packをダウンロードし、インストールする.

### 設定する

USBデバイスを接続してから、VirtualBoxを起動し、「Settings」→ 「Ports」→ 「USB」のところへ行く.

「Enable USB Controller」にチェックを入れ、USB2.0を選択する.

「USB Device Filters」のところを右クリックし、「Add Filter From Device」をクリック.

すると、接続可能なUSB機器が出てくるので選択する.

### Ubuntuで確認

Ubuntuを起動する.



### 参考記事

* [VagrantでUSBを使う - how to code something](http://d.hatena.ne.jp/seinzumtode/20140505/1399317508)
