Title: ubuntuにrosをいれる
Date: 2017-09-25
Slug: ros_ubuntu
Category: ノート
Tags: ubuntu, ros

### インストール

```
# sources.listを設定する
% sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'

# 鍵の設定
% wget http://packages.ros.org/ros.key -O - | sudo apt-key add -

# インストール
% sudo apt-get update
% sudo apt-get install ros-indigo-desktop-full

# rosdepの初期化
% sudo rosdep init
% rosdep update

# 環境設定
% echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
% source ~/.bashrc

# rosinstall
% sudo apt-get install python-rosinstall
```

### セットアップ
