Title: dlibを使って顔検出
Date: 2017-10-08
Slug: face_detection
Category: プログラミング
Tags: python, opencv, dlib

### opencvを使って顔検出し、そこから機械学習を利用して顔認識をする

いろいろ調べたら、dlibというライブラリがあることもあることがわかった.

dlibは機械学習のライブラリで、画像処理などもすることができる.

顔検出は、Opencvのカスケードが精度が良いらしい.

その検証動画が[こちら](https://www.youtube.com/watch?v=LsK0hzcEyHI)

そして、[こんなライブラリ](https://github.com/ageitgey/face_recognition)を見つけた.

顔認識が簡単にできるようになっているライブラリだったので、とりあえず使ってみる.

### インストール

##### ※環境
* Mac : Sierra 10.12.6
* Python : 3.5.3

ドキュメントにある通りに、boost-pythonとdlibと共にface_recognitionをインストールする

```
% brew install boost-python --with-python3 --without-python

% pip install dlib
% pip install face_recognition
```

### 特徴

* 写真の中から顔を見つける
* 顔の中での目、鼻、口、顎の位置を見つける
* 顔認識(見つけた顔が誰の顔かを認識する.)

### コマンドラインから試す

まず、コマンドを使って顔認識を試す.

これを行うためには、人の画像が入ったフォルダを作る必要がある. そして、そこに顔の画像を入れ、ファイル名には`Obama.jpg`のように、名前をつける.認識したい人の顔一人につき、一枚の画像があれば十分である.

そして次に、また別のフォルダをつくる. このフォルダには、顔認識を試してみたい人の画像をいれる.これについては、ファイルの名前については気にする必要はない.

これで準備ができたので、コマンドを打つ.(今回、人の名前つきのフォルダの名前を「img_people」、顔認識を試した人の画像が入ったフォルダの名前を「img_unknown_people」とした)

`% face_recognition ./img_people ./img_unknown_people`

このようにコマンドを打つと、顔認識の結果が出力される.

だれにもマッチしなかったときは、unknown_presonと出る.

たとえば、

`% face_recognition --tolerance 0.5 ./img_people ./img_unknown_people`

のように、toleranceオプションをつけると、顔を認識する閾値のようなものを調整することができる.

デフォルトでは、0.6となっていて、数字を低くするほど、認識を厳しくすることができる.私の場合、この数字を0.5にするだけでいくつかの画像でunknown_personとでるようになった.

この0.5などの数字は、顔の画像同士の"距離"を表していて、0に近いほど、似た顔という計算になっている.それぞれの顔がどのくらいの距離かを確認したい場合は、以下のようなオプションを付けて確認することができる.

(距離は、HOG特徴量を画像から計算して導出している.)

`% face_recognition --torance 0.5 --show-distance true ./img_people ./img_unknown_people`

### Pythonで試す

Githubのface_recognitionのexampleフォルダにあるPythonファイルを実行して試してみる.

#### 画像の中から顔検出

`% python find_faces_in_picture.py`

実行すると、画像の中から顔の部分だけを切り取ったものが表示される.

 より正確な顔検出を試したい場合、

 ` % python find_faces_in_picture_cnn.py`

 を実行する. これはCNNという深層学習で用いるモデルを用いて顔検出を行うので、より正確に検出を行うことができる.

 実際、普通の顔検出で顔でない部分が誤検出されていたが、CNNを用いたものでやってみると、誤検出はなくなった.

#### 画像で顔認識

`% python recognize_faces_in_picture.py`

ファイルの中身を説明すると

1. アメリカの政治家オバマ、バイデン（オバマ大統領時の副大統領)の画像を読み込む
2. 新しい画像（オバマの画像）と手順1で読み込んだ2画像との"距離"を計算し、新しい画像が誰の顔か判定する

#### 顔の特徴をとる

`% python find_facial_features_in_picture.py`

実行すると、顎の位置(chin)、眉毛の位置(left_eyebrow, right_eyebrow)、鼻の筋（nose_bridge)、鼻の先端(nose_tip)、目(left_eye, right_eye)、唇(top_lip, bottom_lip)の座標がコンソールに表示される

また、それらの位置が示された画像も表示される.
