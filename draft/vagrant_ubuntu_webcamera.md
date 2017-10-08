Title: Macにvagrant経由で入れたUbuntu14.04でMacの内蔵カメラを使う
Date: 2017-10-1
Slug: vagrant_ubuntu_webcamera
Category: Ubuntu
Tags: Ubuntu, ROS

### Extensionパックのダウンロード

[このページ](https://www.virtualbox.org/wiki/Downloads)から、Extension Packをダウンロードし、インストールする.

### 調査

いろんなサイトを調べてたらどうやらVBoxManageコマンドを使えばできることがわかった.

しかし、そのときに、VMの名前が必要になるのだが、VMの名前が数字の連なりになってたので、とりあず単純な名前にする

### VMの名前変更

VirtualBoxのGUIを使って変更もできたが、コマンドラインで行った

`% VBoxManage modifyvm "ORIGINAL" --name "NEWNAME"`
