Title: ROSの基礎１ - 用語の理解~サンプル -
Date: 2017-09-30
Slug: ros_fundamental1
Category: ROS
Tags: Ubuntu, ROS

ROSの勉強は、[このフリーの本](http://irvs.github.io/rosbook_jp/)を使って行う.

これは、九州大学の倉爪研究室が書いたものらしい.

ありがたく使って勉強する.

### マスターとノード

* マスター : すべてのノードの管理. 起動していないと何もできない.
* ノード : ROSの最小の構成要素. 一つのノードに一つのロボットの機能があるイメージ

### ノード間のメッセージ通信

全体のプログラムは、ノード同士が通信を行うことによって動く.そのときに行うのが、メッセージ通信である.

ノード間のメッセージ通信には、トピックメッセージ通信とサービスメッセージ通信の2種類がある.

また、メッセージを送信することをROSでは「配信」、受信することを「購読」という.

* トピックメッセージ通信 : 名前の通り、トピックという形式でメッセージを送受信すること. 1対多の通信が可能.
* サービスメッセージ通信 : サービスクライアントとサービスサーバー間の双方向の通信. 1対1のみの通信.

ここで少しわかりずらいので、サービスについて説明する.

#### サービスとは

以下の2つによって構成されている.

サービスクライアント : サービスを送信し情報を受け取る. リクエストを出力し、レスポンスを受け取る.
サービスサーバー : リクエストを受け取ると情報を送信する. リクエストを受取、レスポンスを出力する.

サービスはトピックとは異なり、一回限りの通信なので、リクエストとレスポンスが完了すると、一旦接続がきれる.

#### マスターの役割

ノード間が通信を行うためには、ノード同士で接続しなければならない. その接続を管理するのが、マスターである.

ノードが生成されたら、「ノードの名前」「トピックやサービスの名前」「URIアドレス」「ポート」「パラメーター」などの情報をマスターに登録.ノードが他のノードと接続したいときは、マスターを介してこれらの情報を得る.

#### マスターの実行

ノード館の接続情報を管理するためにマスターを起動しなければいけない.それが以下のコマンドである.

```
% roscore
```

#### 購読者ノード、配信者ノードの実行

rosrunもしくはroslaunchコマンドにより実行する. 前述したとおり、実行時に、自分の情報をマスターに登録する.

```
% rosrun PACKAGE_NAME NODE_NAME
% roslaunch PACKAGE_NAME LAUNCH_NAME
```

購読者ノードは、マスターから配信者ノードの情報を取得する. その情報にもとづき、配信者ノードに直接接続を要請する.

配信者ノードは要請に対する応答として、自身のURIアドレスとポートを送信する.その後、TCPROSという方式を用いて通信する.

#### メッセージ通信の例

[ROSのセットアップ記事](https://maro525.github.io/blog/ros_setup.html)で行ったサンプルを例にとる.

* 配信者 : turtle_teleop_key(キーボードの情報を送信)
* 購読者 : turtlesim_node(キーボードの情報を受信)
* トピック : 「/turtle/cmd_vel」

### ファイルシステム

ROSをインストールすると、/optフォルダにrosというフォルダが作成され、その中にロボット関連のライブラリが入る.触ることはほぼない.

基本的に作業するフォルダは、`~/catkin_ws/src`である.作業フォルダは、パッケージを保存しビルドする場所.

(パッケージは、何かしらを動かすためのひとまとまりプログラムと考えればいい. 自分で書いたものを公開することもできる.)

### ビルドシステム

CMakeを基本的には利用している. catkinビルドシステムは、CMakeをROSにあわせて改良したものである.

#### パッケージを作成してみる

パッケージを作成するコマンドは以下である.

`% catkin_create_pkg [PACKAGE_NAME] [依存するパッケージ1] ... [依存するパッケージN]`

実際にコマンドをうつ

```
% cd ~/catkin_ws/src
% catkin_create_pkg my_first_ros_pkg std_msgs roscpp
```

std_msgsとroscppが今回依存するパッケージとして指定したものである.

パッケージを作成すると、`~/catkin_ws/src`下に、`my_first_ros_pkg`というフォルダが作成される.

そのフォルダの中にある`package.xml`は、パッケージの名前や著作者、ライセンス、依存性パッケージなどの情報を保持している.

`CMakeList.txt`ファイルには、ビルドの設定などを記入する. 作成するパッケージ名、依存パッケージ名、実行可能ファイル名などを設定する.

##### CMakeList.txtの文法について

|言葉|意味|例|
|:---|:---|:---|
|find_package|パッケージを探す|find_package(catkin REQUIRED COMPONENTS roscpp std_msgs)|
|catkin_python_setup()|Pythonを使用するときに用いる|   |
|add_message_files|メッセージファイルを追加するため|add_message_files(FILES Message1.msg Message2.msg)|
|add_service_files|サービスファイルを追加する|add_service_files(FILES Service1.srv Service2.srv)|
|generate_messages|依存しているメッセージを設定|generate_messages(DEPENDENCIES std_msgs)|
|catkin_package|catkinビルドオプション|catkin(INCLUDE_DIRS include LIBRARIES my_first_ros_pkg CATKIN_DEPENDS roscpp std_msgs DEPENDS system_lib)|

* INCLUDE_DIRSでヘッダーファイルの置かれたフォルダを指定.
* LIBRARIESで「add_library」で指定したライブラリの保存先フォルダをしてい. このLIBRARIESフォルダは、`catkin_ws/devel/liv`下に作成される
* CATKIN_DEPENDSはroscppやstd_msgsなど、依存するパッケージを指定
* DEPENDSはシステム依存のパッケージを指定

|言葉|意味|例|
|:---|:---|:---|
|include_directories|インクルードフォルダを指定|include_directories(${catkin_INCLUDE_DIRS})|
|add_library|作成するライブラリを指定.ファイルと作成するライブラリを記述|add_library(my_first_ros_pkg src/${PROJECT_NAME}/my_first_ros_pkg.cpp)|
|add_executable|作成する実行可能ファイルを指定する|add_executable(my_first_ros_pkg_node src/my_first_ros_pkg_node.cpp)|
|add_dependencies|ビルドする前にメッセージのヘッダーファイルを作成する必要がある場合|add_dependencies(my_first_ros_pkg_node my_first_ros_pkg_generate_message_cpp)|
|target_link_libraries|リンクするライブラリを指定|target_link_libraries(my_first_ros_pkg_node ${catkin_LIBRARIES})|

まだこの時点では実感がわかずよくわからない.これらは、使っていくうちに覚えていくしかない.

##### 例

```
cmake_minimum_required(VERSION 2.8.3)
project(my_first_ros_pkg)
find_package(catkin REQUIRED COMPONENTS roscpp std_msgs)
catkin_package( INCLUDE_DIRS include
    CATKIN_DEPENDS roscpp std_msgs
    DEPENDS system_lib
)
include_directories( ${catkin_INCLUDE_DIRS} )
add_executable(hello_world_node src/hello_world_node.cpp)

add_executable(hello_world_node src/hello_world_node.cpp)
add_dependencies(hello_world_node my_first_ros_pkg_generate_messages_cpp)
target_link_libraries(hello_world_node ${catkin_LIBRARIES})
```

CMakeList.txtをこのような形にする.

add_executableの部分でhello_world_node.cppというファイルからhello_world_nodeという実行ファイルを作成するとしたので、`~/catkin_ws/src/my_first_ros_pkg/src`下に、hello_world_node.cppというファイルを作成する.

ファイル名:hello_world_node.cpp
```
#include <ros/ros.h>
#include <std_msgs/String.h>
#include <sstream>
int main(int argc, char **argv)
{
   ros::init(argc, argv, "hello_world_node");
   ros::NodeHandle nh;
   ros::Publisher chatter_pub =
     nh.advertise<std_msgs::String>("say_hello_world", 1000);
   ros::Rate loop_rate(10);
   int count = 0;
   while (ros::ok())
   {
     std_msgs::String msg;
     std::stringstream ss;
     ss << "hello world " << count;
     msg.data = ss.str();
     ROS_INFO("%s", msg.data.c_str());
     chatter_pub.publish(msg);
     ros::spinOnce();
     loop_rate.sleep();
     ++count;
  }
  return 0;
}
```

###### ビルドする

`% cd ~/catkin_ws && catkin_make`

ROSセットアップ時にエイリアスをしているので、「cm」と入力するだけでもビルドできる.

ビルドが終了すると、`~/catkin_ws/devel/lib/my_first_ros_pkg`に「hello_world_node」という名前のファイルが作成されている

###### 実行する

まず、roscoreの実行

`% roscore`

別のウインドウを開き、ノードを実行

```
% rosrun my_first_ros_pkg hello_world_node
[INFO] [1423443540.131775283]: hello world 0
[INFO] [1423443540.231826916]: hello world 1
[INFO] [1423443540.331798085]: hello world 2
[INFO] [1423443540.431796634]: hello world 3
[INFO] [1423443540.531808660]: hello world 4
[INFO] [1423443540.631800431]: hello world 5
[INFO] [1423443540.731805683]: hello world 6
```

ノードを実行すると、hello world 0,1,2などメッセージが配信されることが確認できる
