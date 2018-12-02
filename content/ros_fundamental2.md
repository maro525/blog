Title: ROSの基礎2 - トピック通信の実践 -
Date: 2017-10-1
Slug: ros_fundamental2
Category: プログラミング
Tags: Ubuntu, ROS

[ROSの基礎1](https://maro525.github.io/blog/ros_fundamental2.html)に引き続き、ROSBOOKにもとづき、ROSの勉強を進めていく.

# トピック通信の実践

## パッケージの作成

```
% cs
% catkin_create_pkg irvs_ros_tutorials std_msgs roscpp
```

## package.xmlの編集

`~/catkin_ws/src/irvs_ros_tutorials/package.xml`を以下のようにする

```
<?xml version="1.0"?>
<package>
  <name>irvs_ros_tutorials</name>
  <version>0.0.0</version>
  <description>The irvs_ros_tutorials package</description>

  <maintainer email="vagrant@todo.todo">vagrant</maintainer>

  <license>TODO</license>

<buildtool_depend>catkin</buildtool_depend>
  <build_depend>roscpp</build_depend>
  <build_depend>std_msgs</build_depend>
  <build_depend>message_generation</build_depend>
  <run_depend>roscpp</run_depend>
  <run_depend>std_msgs</run_depend>
  <run_depend>message_runtime</run_depend>

  <export>
  </export>
</package>
```

## CmakeLists.txtの編集

`~/catkin_ws/src/irvs_ros_tutorials/CmakeLists.txt`を以下のように編集する

```
cmake_minimum_required (VERSION 2.8.3)
project(irvs_ros_tutorials)

## catkinビルドに必要なコンポーネントパッケージの設定
find_package(catkin REQUIRED COMPONENTS roscpp std_msgs message_generation)

## メッセージファイルの設定
add_message_files(FILES msgTutorial.msg)

## サービスファイルの設定
add_service_files(FILES srvTutorial.srv)

## add_message_filesで使用するメッセージの依存関係を設定
generate_messages(DEPENDENCIES std_msgs)

## インクルートディレクトリ、ライブラリ、catkinビルド、システムに依存するパッケージの指定
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES irvs_ros_tutorials
  CATKIN_DEPENDS roscpp std_msgs
  DEPENDS system_lib
)

## インクルードディレクトリの設定
include_directories(include ${catkin_INCLUDE_DIRS})

## ros_tutorial_msg_publisherノードの設定
## 実行ファイル、ターゲットリンクライブラリ、追加の依存関係などを設定
add_executable(ros_tutorial_msg_publisher
  src/ros_tutorial_msg_publisher.cpp)
target_link_libraries(ros_tutorial_msg_publisher ${catkin_LIBRARIES})
add_dependencies(ros_tutorial_msg_publisher
  irvs_ros_tutorials_generate_messages_cpp)

## ros_tutorial_msg_subscriberノードの設定
## 実行ファイル、ターゲットリンクライブラリ、追加の依存関係などを設定
add_executable(ros_tutorial_msg_subscriber
  src/ros_tutorial_msg_subscriber.cpp)
target_link_libraries(ros_tutorial_msg_subscriber ${catkin_LIBRARIES})
add_dependencies(ros_tutorial_msg_subscriber
  irvs_ros_tutorials_generate_message_cpp)
```

## メッセージファイルの作成

CMakeLists.txtで`add_message_files(FILES msgTutorial.msg)`のようなオプションを追加したので、ファイルを作成する

```
% roscd irvs_ros_tutorials
% mkdir msg
% cd msg
% vim msgTutorisl.msg
```

そして、ファイルの中身は以下のようにする.

```
int32 data
```

32ビット整数型メッセージ形式のdata変数を定義している

## 配信者ノードの作成

CMakeLists.txtで`add_executable(ros_tutorial_msg_publisher src/ros_tutorial_msg_publisher.cpp)`のように、配信者ノードの実行ファイルを生成するオプションを追加したので、そのためのプログラムを作成する

```
% roscd irvs_ros_tutorials/src
% vim ros_tutorial_msg_publisher.cpp
```

ファイルの中身は以下のようにする

```
#include "ros/ros.h"
#include "irvs_ros_tutorials/msgTutorial.h"

int main(int argc, char **argv)
{
  // ノード名の初期化
  ros::init(argc, argv, "ros_tutorial_msg_publisher");
  // ROSシステムとの通信のためのノードハンドルを宣言
  ros::NodeHandle nh;

  // 配信者ノードの宣言
  // irvs_ros_tutorialsパッケージのmsgTutorialメッセージファイルを利用した配信者ros_tutorial_pubを宣言する
  // トピック名をros_tutorial_msgとし、配信者キュー( queue )のサイズを100に設定する
  // 配信者キューには、メッセージを送る際、メッセージデータを蓄積する
  ros::Publisher ros_tutorial_pub = nh.advertise<irvs_ros_tutorials::msgTutorial>("ros_tutorial_msg", 100);

  // ループの周期を設定する. "10"は10Hzを表す
  ros::Rate loop_rate(10);
  // メッセージに使用する変数の宣言
  int count = 0;

  // ros::ok()はROSの動作が正常であるならtrueを返す関数である
  while (ros::ok())
  {
    // msgTutorialメッセージファイル形式でmsg変数を宣言する
    irvs_ros_tutorials::msgTutorial msg;

    // 変数countを使用して、メッセージの値を定める
    msg.data = count;

    // ROS_INFOというROS関数を使用して、count変数を表示する
    ROS_INFO("send msg = %d", count);

    // メッセージを発行する.
    ros_tutorial_pub.publish(msg);

    // 上で定められたループサイクルになるように、スリープに入る
    loop_rate.sleep();

    // count変数に1ずつ追加
    ++count;
  }

  return 0;
}
```

## 購読者ノードの作成

配信者ノードと同じような手順で、購読者ノードも追加する.

ファイル名 : ros_tutorial_msg_subscriber.cpp

```
#include "ros/ros.h"
#include "irvs_ros_tutorials/msgTutorial.h"

// メッセージを受信したときに動作するコールバック関数を定義
void msgCallback(const irvs_ros_tutorials::msgTutorial::ConstPtr& msg)
{
  // 受信したメッセージを表示する
  ROS_INFO("recieve msg: %d", msg->data);
}

int main(int argc, char **argv)
{
  // ノード名の初期化
  ros::init(argc, argv, "ros_tutorial_msg_subscriber");
  // ROSシステムとの通信のためのノードのハンドルを宣言
  ros::NodeHandle nh;

  // 購読者ノードの宣言
  // irvs_ros_tutorialパッケージのmsgTutorialメッセージファイルを利用した購読者ros_tutorial_subを宣言する
  // トピック名をros_tutorial_msgとし、購読者キュー( queue )のサイズを100に設定する
  ros::Subscriber ros_tutorial_sub = nh.subscribe("ros_tutorial_msg", 100, msgCallback);

  // メッセージが受信されるまで待機し、受信が行われた場合、コールバック関数を実行する
  ros::spin();

  return 0;
}
```

## ビルド

エイリアスがしてあれば一発でできる

`% cm`

このコマンドにより、「~/catkin_ws/build」と「~/catkin_ws/devel」フォルダにファイルが生成される

* ~/catkin_ws/build : catkinビルドで使用された設定内容の保存
* ~/catkin_ws/devel/lib/irvs_ros_tutorials : 実行ファイル
* ~/catkin_wd/devel/include/irvs_ros_tutorials : メッセージファイルから自動生成されたヘッダーファイルが保存される

## 実行

まず、マスターを起動

`% roscore`

## 配信者ノードの実行

`% rosrun irvs_ros_tutorials ros_tutorial_msg_publisher`

実行すると、トピックメッセージの内容が表示される

## 購読者ノードの実行

`% rosrun irvs_ros_tutorials ros_tutorial_msg_subscriber`

実行すると受け取ったデータが表示される

## 通信状態の確認

`% rqt_graph`

これによりトピック通信の実践ができた.
