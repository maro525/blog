### RViz

* 3次元の可視化ツール
* 距離センサーから得られる距離データ、3次元センサから得られるPoint Cloud Data,カメラから取得したカラー画像が表示できる.
* ロボットの位置や姿勢をユーザーが自由に指定でき、インタラクティブなノードととユーザー間の情報のやり取りが可能になる
* 3次元モデルの記述法であるURDF(Unified Robot Description Format)フォーマットでロボットを定義し、それを表示する
* さらに、関節自由度や移動自由度を定義しておけば、ロボットの位置や姿勢を自由に変更できる

### rqt

* GUI開発ツール
* 各ノードの階層構造をグラフで表示や現在のノードとトピックの状態を確認できるツール、トピックデータを2次元プロットで図示するツールがある

### パッケージの使用

1. 公式サイトで探したいパッケージを検索する
2. ビルドシステムがcatkinかrosbuildか、誰が製作したものか、オープンソースのライセンスの種類などのなどの情報を確認し、注意深く検討する
3. 依存パッケージのインストール
4. パッケージ本体のインストール. バイナリをインストールする方法する方法とソースをインストールしてそれをビルドする方法がある
