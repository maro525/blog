Title: Mac Sierra でOpencv使う
Date: 2017-08-23
Slug: mac_opencv
Category: Mac
Tags: python, opencv

最近のHomebrewのアップデートにより、opencvのバージョン3.3.0が導入されたが、Pythonでopencvを使ってみたところ、libjpegが見つからないとエラーが出た.

そこで、ぐぐってでてきた[Qiita記事](http://qiita.com/hurutoriya/items/d162f9f50be6d2088630)を参考に、libjpegとのシンボリックをはったら、正常にopencvを実行できた.

しかし、pythonのコンソールで`import cv2`したあとに、`cv2.__version__`を確認すると、3.2.0と表示された.

そこで、`brew info opencv3`として、内容を確認してみると、3.2.0と3.3.0のパッケージが両方入っていることがわかった.

そこでいろいろ調べてみると、

homebrewのパッケージの過去のversionを削除したい場合は、`brew cleanup opencv3`のようにすればいいことがわかったが、brewのパッケージについてもう少しいろいろ調べてみた.

また、[`brew versions`コマンドを使う](http://qiita.com/ysk24ok/items/91a7c502d33e0dde72ad)のような手段もによって、brewからインストールしたパッケージのバージョンを変更しながら使えることもわかった.

その後、[この記事](http://qiita.com/neriai/items/0d9b3dd4344bdfa428f8)から、Homebrewにおいて、Opencvの2系と3系のリポジトリが統一され、Opencv3に統一されたとのことだったので、一旦Homebrewからopencv3を削除してから、また新しく入れることにした.

ということで、一旦削除する.

```
brew unisntall --force opencv3
```

実際に`brew info`で確認してみた.

```
% brew info opencv
opencv: stable 3.3.0 (bottled)
Open source computer vision library
http://opencv.org/
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/opencv.rb
==> Dependencies
Build: cmake ✔, pkg-config ✔
Required: eigen ✔, ffmpeg ✔, jpeg ✔, libpng ✔, libtiff ✔, openexr ✔, numpy ✔
==> Requirements
Recommended: python3 ✔
==> Options
--without-python
	Build without python2 support
--without-python3
	Build without python3 support




% brew info opencv3
homebrew/science/opencv3: stable 3.3.0 (bottled), HEAD [keg-only]
Open source computer vision library, version 3
http://opencv.org/
Not installed
From: https://github.com/Homebrew/homebrew-science/blob/master/opencv3.rb
==> Dependencies
Build: cmake ✔, pkg-config ✔
Recommended: eigen ✔, jpeg ✔, libpng ✔, libtiff ✔, openexr ✔, numpy ✔
Optional: ffmpeg ✔, gphoto2 ✘, gstreamer ✔, jasper ✘, jpeg-turbo ✘, libdc1394 ✘, openni ✘, openni2 ✘, qt ✔, tbb ✔, vtk ✘
==> Requirements
Optional: cuda ✘, java ✔, python3 ✔
==> Options
--c++11
	Build using C++11 mode
--with-contrib
	Build "extra" contributed modules
--with-cuda
	Build with CUDA v7.0+ support
--with-examples
	Install C and python examples (sources)
--with-ffmpeg
	Build with ffmpeg support
--with-gphoto2
	Build with gphoto2 support
--with-gstreamer
	Build with gstreamer support
--with-jasper
	Build with jasper support
--with-java
	Build with Java support
--with-jpeg-turbo
	Build with libjpeg-turbo instead of libjpeg
--with-libdc1394
	Build with libdc1394 support
--with-nonfree
	Enable non-free algorithms
--with-opengl
	Build with OpenGL support (must use --with-qt)
--with-openni
	Build with openni support
--with-openni2
	Build with openni2 support
--with-python3
	Build with python3 support
--with-qt
	Build the Qt backend to HighGUI
--with-static
	Build static libraries
--with-tbb
	Enable parallel code in OpenCV using Intel TBB
--with-vtk
	Build with vtk support
--without-eigen
	Build without eigen support
--without-jpeg
	Build without jpeg support
--without-libpng
	Build without libpng support
--without-libtiff
	Build without libtiff support
--without-numpy
	Use a numpy you've installed yourself instead of a Homebrew-packaged numpy
--without-opencl
	Disable GPU code in OpenCV using OpenCL
--without-openexr
	Build without openexr support
--without-python
	Build without Python support
--without-test
	Build without accuracy & performance tests
--HEAD
	Install HEAD version
==> Caveats
This formula is keg-only, which means it was not symlinked into /usr/local,
because opencv3 and opencv install many of the same files.
```

infoのところにあるURLに飛んでみたところ、opencv3の方のリンクが切れていたので、Homebrewのopencvはバージョン3に統一されたことが確認できた.

また、以前までは、openvとopencv3のバージョンが違ったため、opencvはkeg-onlyとなっていたので、`brew link opencv3 --force`のようにして使う必要があったが、これからはその必要がなくなりそう.

### 再びopencvインストール

```
% brew install opencv
```

僕は、pyenv-virtualenvからpythonを扱っているので、pyenvの環境にシンボリックリンクをはる.(NAMEの部分は、virtualenvの環境の名前)

```
% ln -sf /usr/local/Cellar/opencv/3.3.0_2/lib/python3.6/site-packages/cv2.cpython-36m-darwin.so ~/.pyenv/versions/3.5.3/envs/NAME/lib/python3.5/site-packages/cv2.so
```

これで挙動を確認してみる.

```
Python 3.5.3 (default, Jun 29 2017, 21:04:15)
[GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.3.0'
>>>
```

となっていたので、問題なくopencvをインストールすることができた.

### 終わりに

brew cleanupの存在をうっすらとしかしらなくて、`brew cleanup`を実行してみたら、1.6GBも容量が減った.

これからは頻繁に`brew cleanup`をしていこうと思う.
