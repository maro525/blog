Title: Kivyの基礎を公式ドキュメントで学ぶ
Date: 2017-10-15
Category: Python
Tags: Kivy
Slug: kivy_fundamental


PythonのGUIライブラリ、Kivyの公式サイトのドキュメントを見て、Kivyの基礎を学んでいく.

[このページ](https://kivy.org/docs/gettingstarted/properties.html)から読んでいく.

このページはメモ代わりである.

### プロパティについて

プロパティは、クラスのメソッドの中ではなく、直接クラスの中で宣言する.

数値、文字列、配列などのプロパティをデフォルトでいくつか宣言できるようになっている.

プロパティは、オブザーバーパターンというプログラミング上の手法を用いている.

### Kv Language

Kivyでは、Kv Launguageという独自の言語を用いる. これはウィジェットをデザインするために用いる. アプリケーションのロジックとデザインを分離するためのものである.

### Events

KivyはEvent-Basedである. プログラムはすべてなんらかのイベントによって定められる.

* Clock events : 一回だけ起こるイベントや一定間隔で起こるイベントを指定したいときに用いる
* Input events : マウスの動きなどに対してのイベント
* Class Events : WidgetとEventを結びつける.
