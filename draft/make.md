Title: Make
Date: 2017-08-11
Slug: make
Category: プログラミング
Tags: Linux

#### make とは
* プログラムのコンパイル、リンク、インストール作業を自動化するツール
* System V Make, mk, nmake, BSD Makeなどさまざまなmakeが存在するが、gmakeが主流？
* `make -v`でコマンド確認

##### すごいところ
* **更新のチェックをできる**
	* ソースコードが実行ファイルやオブジェクトファイルよりも新しい場合にコンパイル. タイムスタンプをチェックしている.
* **依存関係のチェックをできる**
	* 上層のファイルのみ更新された場合上層のファイルのみ再コンパイル
* 自動変数機能
	* $@ = ルールのターゲットのファイル名
	* $< = 最初の依存関係の名前
* サフィックスルール
	* q

#### Makefile
##### 基本の書き方


>[作りたいもの]: [材料]

>(-----TAB-----) [作り方]

#####  具体的な例
main.cとsub.cという2つのソースからmyprogramという実行バイナリを生成するとき、

> gcc main.c sub.c -o myprogram

とする. しかし、これだと一方のファイルを修正しただけですべてのソースファイルのコンパイルが必要となるので

> gcc -c main.c
> gcc -c sub.c
> gcc main.o sub.o -o myprogram

のように別々にオブジェクトファイルを作成して、最後にリンクしたほうが効率的である.

後者をMakefileにすると以下のようになる

```
myprogram: main.o sub.o
			gcc -o myprogram main.o sub.o
main.o:    main.c
			gcc -c main.c
sub.o:     sub.c
			gcc -c sub.c
```

makeコマンドは、

> make [作りたいもの]

として実行するのが基本だが、[作りたいもの」を省略した場合は、一番最初にある[作りたいもの]が作成される.つまり、

> make

とすれば、myprogramを作成することになる.
myprogramの材料であるmain.oとsub.oが存在しない場合、再帰的にオブジェクトファイルがコンパイルされる.

###### つまり、Makefileは、ファイルを作りたいときに、その材料はどれなのか、どうやって作るのかという関係性を記述したものである.

##### 一般的なMakefileの例

```
CC       = gcc
CFLAGS   = -O4 -Wall -I/usr/local/include
DEST     = /usr/local/bin
LDFLAGS  = /-L/usr/local/lib
LIBS     = -lhoge -lm
OBJS     = main.o sub.o
PROGRAM  = myprogram

all:       $(PROGRAM)

$(PROGRAM) $(OBJS)
           $(CC) $(OBJS) $(LDFLAGS) $(LIBS) -o $(PROGRAM)

clean:;    rm -f *.o *~ $(PROGRAM)

install:   $(PROGRAM)
           install -s $(PROGRAM) $(DEST)
```

* 最初の「◯＝○」という記述は、あとで使う変数の定義
* `$(PROGRAM)~`より以下は、実行バイナリを作成する部分.
* `install:~`以下は、材料、作り方が揃っているが、これは、makeとしては「installを作成しろ」と解釈する. installを作成するためには、$(PROGRAM)が必要で、作り方は、実行バイナリを所定のディレクトリにコピーすること
* allの部分は、$(PROGRAM)をつくれという意味しかない. これは、作りたいものを指定せずにmakeコマンドを実行すると一番最初にある[作りたいもの]が生成されることを利用して、makeコマンドを打てばとりあえず実行バイナリが作成されるようにするための仕掛け.このルールは一番最初に書いておく.



#### 参考URL
* [Makefileの解説](http://omilab.naist.jp/~mukaigawa/misc/Makefile.html)
* [zagaku2006_make_ver5.pdf](http://www.ep.sci.hokudai.ac.jp/~epnetfan/zagaku/2006/0630/pub/zagaku2006_make_ver5.pdf)
*
