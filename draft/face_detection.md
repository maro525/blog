Title: dlibを使って顔検出
Date: 2017-09-17
Slug: face_detection
Category: プログラミング
Tags: python, opencv, dlib

### opencvを使って顔検出し、そこから機械学習を利用して顔認識をする

いろいろ調べたら、dlibというライブラリがあることもあることがわかった.

dlibは機械学習のライブラリで、画像処理などもすることができる.

顔検出は、Opencvのカスケードが精度が良いらしい.

その検証動画が![こちら](https://www.youtube.com/watch?v=LsK0hzcEyHI)

そしてあるとき、![こんなライブラリ](https://github.com/ageitgey/face_recognition)を見つけた.

顔認識が簡単にできるようになっているライブラリだったので、とりあえず使ってみる.

#### インストール

ドキュメントにある通りに、boost-pythonとdlibをインストールする

```
% brew install boost-python --with-python3 --without-python

% pip install dlib
% pip install face_recognition
```
