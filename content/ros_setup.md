Title: Ubuntu14.04にROSをいれる
Date: 2017-09-30
Slug: ros_setup
Category: プログラミング
Tags: Ubuntu, ROS

# インストール

```
# sources.listを設定する
# ROSリポジトリアドレスの追加
% sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'

# 鍵の設定
# ROSリポジトリからパッケージをダウンロードするための公開鍵を追加
% wget http://packages.ros.org/ros.key -O - | sudo apt-key add -

# インストール
% sudo apt-get update
% sudo apt-get install ros-indigo-desktop-full
% sudo apt-get install ros-indigo-rqt*

# rosdepの初期化
% sudo rosdep init
% rosdep update

# rosinstallのインストール
% sudo apt-get install python-rosinstall

# 環境設定ファイルの読み込み
% source /opt/ros/indigo/setup.bash
```

# セットアップ

## 作業フォルダの作成と初期化

ROSでは、catkinというROS専用のビルドシステムを使っていて、これを使用するためにcatkin作業フォルダを作成する必要がある.

* catkinは、「キャッン」と読む

この設定はインストール時に一度したら、それ以降はする必要はない

```
% mkdir -p ~/catkin_ws/src
% cd ~/catkin_ws/src
% catkin_init_workspace
```

この時点では、catkin作業フォルダには、srcフォルダとその中のCMakeList.txtしか入っていない.

## ビルド

```
% cd ~/catkin_ws/
% catkin_make
```

問題なく完了すると、catkin_wsフォルダの中身が以下のようになる

```
% ls
build devel src
```

buildフォルダ、develフォルダが作成される.

buildフォルダには、ビルドシステムに関連するファイル、develフォルダには、実行に関連するファイルが保存される.

## .bashrcに設定を書く

Ubuntuを実行するたびに環境設定を読み込むのは煩わしいので、環境設定ファイルを読み込むように設定する.

~/.bashrcの中身を編集する.

すでにたくさんの環境設定がたくさんなされているので、それは消さずに下の方に記述を追加する.

vimやgedit,emacs,nano等のエディタで編集する

```
% vim ~/.bashrc
```

## .bashrcへの追加部分

```
# Set ROS Indigo
source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash

# Set ROS alias command
alias cw=’cd ~/catkin_ws’
alias cs=’cd ~/catkin_ws/src’
alias cm=’cd ~/catkin_ws && catkin_make’
```

これを書き込んだあとに設定を反映させるためには、`source ~/.bashrc`とコマンドを打つ

以後は、ubuntu起動時に.bashrcが読み込まれるため、rosの設定は自動で反映される

# サンプル

## マスター起動

`% roscore`

## シミュレーター起動

別ウインドウでubuntuに入って以下のコマンド

`% rosrun turtlesim turtlesim_node`

GUIに亀が表示される

![No Image!]({filename}/image/ros_turtlesim.png)

## 操作のためのコンソール起動

`% rosrun turtlesim turtle_teleop_key`

キーを押すと、亀が上下へ動く様子が確認できる

# 実行中のノードの確認

`% rosrun rqt_graph rqt_graph`

![No Image!]({filename}/image/ros_node.png)

実行中のノードごとのつながりがわかるようになっている.

問題なく動いたのでOKとする.詳しい勉強は後ほど.

以上で、基本的な導入はできた.

# 参考
* [仮想マシン上で手軽にROSのサンプルを動かしてみる - Qiita](https://qiita.com/ryskiwt/items/13451d46805ac2e9459c)
