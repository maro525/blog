Title: PythonのGUIライブラリのKivyを使ってみる
Date: 2017-10-14
Category: Python
Tags: Kivy
Slug: kivy

**Kivy = PythonのマルチプラットフォームGUIライブラリ**

そして、MITライセンスで使いやすい形になっている.

### 環境　
Mac Sierra 10.12.6

Python3.5.3

Kivy 1.10.0

### インストール
[公式のページ](https://kivy.org/docs/installation/installation-osx.html)にあるように、Homebrewとpipをつかった方式でインストールしていく.

1.Homebrewで必要なものをインストール

`brew install pkg-config sdl2 sdl2_image sdl2_ttf sdl2_mixer gstreamer`

おそらく、sdl2はOpenGLを扱うためにインストールが必要

2.pipでCythonとkivyをインストール

(pyenv-virtualenvで仮想環境を作ってから実行)

`pip install -U Cython`

`pip install kivy`

pythonコンソールに移動して、`import kivy` と打ってみてエラーができなければOK

iOS環境で動作させたいときはまた別に[いろいろ準備](https://github.com/kivy/kivy-ios)が必要だがとりあえずパス.

### あとでやってみる
* iOSで動かす

### 基本を[公式のドキュメント](https://kivy.org/docs/guide/basic.html#quickstart)から学ぶ

### Kiyyの最小構成　

```
import kivy
kivy.require('1.10.0')

from kivy.app import App
from kivy.uix.button import Button

class MyApp(App):

    def build(self):
        return Button(text='Hello World')

if __name__ == '__main__':
    MyApp().run()

```
1. Appクラスのサブクラスを作る
2. buildというメソッドでウィジェット(説明するとしたら機能を持った部品？)を作る
3. Appクラスでrun()させる

4行目: Appクラスのインポート. Appクラスのコードは`.pyenv/versions/NAME/lib/python3.5/site-package/kivy/`以下にある. Appクラスが何をしてるか知りたいとき参考になるかも

5行目: uixモジュールからボタンウィジェトを読み込む. uixモジュールは、ユーザーインターフェスを持っている.

7行目: kivyのAppの基本となるクラスを定義

9,10行目: Appクラスのbuileメソッドでウィジェットをreturnすることで、Buttonウィジェットがこのアプリでの'Root Widget'(一番根本のウィジェット的な？)になる.

12,13行目: MyAppのインスタンスを作って実行している. (インスタンスを作らなきゃいけないので、`MyApp.run()`だとエラー)

ターミナル等で`python ファイル名.py`で実行すると以下のようなウインドウが表示される

![No Image!]({filename}/image/quickstart_kivy.png)

### カスタマイズしていく

##### ユーザー名とパスワードを入力するインターフェースを作る

コードは以下のような感じ
```
from kivy.app import App
from kivy.uix.gridlayout import GridLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput

class LoginScreen(GridLayout):

    def __init__(self, **kwargs):
        super(LoginScreen, self).__init__(**kwargs)
        self.cols = 2
        self.add_widget(Label(text='User Name'))
        self.username = TextInput(multiline=False)
        self.add_widget(self.username)
        self.add_widget(Label(text='password'))
        self.password = TextInput(password=True, multiline=False)
        self.add_widget(self.password)


class MyApp(App):

    def build(self):
        return LoginScreen()

if __name__ == '__main__':
    MyApp().run()
```

2行目: uixモジュールからGridLayoutを読み込み

6行目: GridLayoutを使ってRoot Widgetを定義

8行目: `__init__`メソッドで今回使うGridLayoutの動作を定義する

9行目:　`__init__`をするとき必ずsuperを呼び、もとのGridLayoutクラスのプロパティやメソッド読み込む必要がある.

* ウインドウの大きさを変えると自動でウィジェットのサイズはリサイズされる


### 参考
* [KivyのGithubのレポジトリ](https://github.com/kivy/kivy)
* [PythonのマルチプラットフォームGUIライブラリkivyを使ってMac, Ubuntu, iOS用のGUIアプリを作る方法 - MyEnigma](http://myenigma.hatenablog.com/entry/2016/05/06/170854)
