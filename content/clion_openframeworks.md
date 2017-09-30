Title: CLionでopenframeworksをビルドする
Date: 2017-08-12
Slug: clion_openframeworks
Category: Openframeworks
Tags: Clion, openframeworks, c++

### 環境(2017/08/12)
* Mac:10.12.6
* CMake:3.8.2
* openframeworks:0.9.8

### 準備
1. [ofxCMake](https://github.com/BildPeter/ofxCMake)をクローンして、addonsフォルダに入れる
2. `ofxCMake/project/CMakeLists.txt`の中で、OFのルートのフォルダを自分のPCのものに書き換える

### 手順
1. `ofxCMake/project/CMakeLists.txt`をプロジェクトのフォルダにコピーする
2. main.cppやofApp.cpp以外にも自分で書いたファイルがあれば、Source Filesに追加する(ヘッダーファイルは追加しなくていいっぽい）
3. APP_NAMEの部分でアプリケーションの名前を変更する
4. CLionのメニューからプロジェクトフォルダを開く
5. プロジェクトのルートフォルダを変更するか聞かれるが無視する
6. gitで'.idea'フォルダのファイルを管理するか聞かれるが、gitに追加する必要なし
7. アドオン追加する（アドオン追加方法は下記)
8. ビルドする

### アドオン追加手順
**Internal Addons(元から入ってるaddon)の場合**

`CMakeList.txt`のAddOnsの部分に書き加える

**External Addonsの場合**

1. ofxNAME.cmake(NAMEはaddonの名前)を`ofxCMake/addOns`内のtemplateファイルからコピーして作成(ファイルの中身はaddonの形態に応じて書き換える)->CMakeの文法について下記
2. `ofxCMake/addOns/external`に1.で作成したファイルをコピーする
3. `ofxCMake/modules/addOns.cmake`の中に、`OF_include_external_addOns( ofxNAME )`を追加
4. プロジェクトのaddOnsの部分にofxNAMEを追加する.

### CMakeの文法(勉強中)
|言葉|意味|
|:---|:---|
|cmake_minimum_required(VERSION 3.8.2|プロジェクトに最低限必要なcmakeのバージョンを指定, 必須|
|add_executable(Main main.cpp)| 生成する実行ファイルとその元となるソースプログラム名を指定する|
|project(TMP)|プロジェクト名設定|
|set(XXX xxx)|キャッシュ変数定義・代入|
|add_library|STATICをつけると静的ライブラリ|
|target_link_libraries|ライブラリを実行ファイルにリンクする|
|add_subdirectory|ディレクトリをcmakeの管理に追加する|
|set_target_properties |   |
|file   |   |

* `set(CMAKE_VERBOSE_MAKEFILE 1)`とすると、makeの実行コマンドが見える
