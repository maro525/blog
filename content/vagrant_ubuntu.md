Title: MacにVagrantでUbuntuをいれる
Date: 2017-09-25
Slug: vagrant_ubuntu
Category: プログラミング
Tags: Ubuntu,Vagrant,Virtualbox

# インストール

`vagrant box add NAME URL`というコマンドでOSをインストールすることができる.

boxの一覧は、![このページ](http://www.vagrantbox.es/)で確認することができる.

今回は、Official Ubuntu 14.04 daily Cloud Image amd64 (Development release, No Guest Additions)をインストールする

```
% vagrant box add ubuntu1404 https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box
```

次に、設定ファイルを作成する

`vagrant init ubuntu1404`

このコマンドを打つと、以下のように表示される

```
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

Vagrantfileが作成されている.


# 設定の変更

Vagrantfileの`congif.vm.network "private_network", ip: "192.168.33.10"`の部分のコメントアウトを外す

# 仮想サーバー立ち上げ

`vagrant up`

# 仮想サーバーに入る

`vagrant ssh`

出るときは、`exit`

# VNCサーバー接続

## 準備
```
% sudo apt-get update
% sudo apt-get install xfce4 ubuntu-desktop vnc4server
```

## 起動

```
# パスワードの設定
% vnspasswd

# VNCサーバー起動
% vncserver :1

# 起動しているか確認
% netstat -an | grep 5901
```

## 設定ファイル変更

`~/.vnc/xstartup`に次の文が入るようにする

```
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

## 接続してみる

MacのFinderから、「サーバーへ接続」

そして、`vnc://192.168.33.10:5901`にアクセス

# vagrant-manager

vagrantのマシンを、メニューバーから操作することができるアプリ

## インストール

`brew cask install vagrant-manager`

# GUIを有効にする

Vagrantfile以下の部分のコメントを外す

```
  config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
     vb.gui = true

     # Customize the amount of memory on the VM:
     vb.memory = "1024"
  end
```

また、~/.bashrcファイルに以下の記述をしていないとエラーがでる.

`export DISPLAY=:0`


#
- ![VagrantとVirtualboxでubuntu環境を構築し、Macからvnc接続する - juncollinの日記](http://juncollin.hatenablog.com/entry/2017/02/18/141358)
