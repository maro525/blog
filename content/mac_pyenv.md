Title: Sierraでpyenv, virtualenvを使う
Date: 2017-01-10
Slug: mac_pyenv
Category: ノート
Tags: python

pyenv,virtualevまわりの話はweb上にたくさん情報が転がっているので、自分の環境セットアップ方法についてメモ代わりに記す

### 環境
Mac: Sierra 10.12.6

### 簡単な説明

* pyenvはpythonのバージョンを切り替えて使用するために利用
* virtualenvは仮想環境を構築するために利用. プロジェクトごとにパッケージを管理するために利用.

### 環境設定

* Homebrewからpyenvとvirtualenvをインストール.

```
% brew install pyenv
% brew install pyenv-virtualenv
```

* .bashrcや.zshrcに書き込む

```
export PYENV_ROOT="${HOME}/.pyenv"
if [ -d "${PYENV_ROOT}" ]; then
    export PATH=${PYENV_ROOT}/bin:$PATH
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"
fi
```

再起動 or `source ~/.zshrc`で、設定を読み込む

* 好きなpythonのバージョンをインストール

```
% pyenv install 3.5.3
```

`pyenv versions`でインストールできたか確認する

### pyenvを使う

```
# フォルダにpythonのバージョンを適用したいとき
pyenv local 3.5.3

# 全フォルダで適用するとき
pyenv global 3.5.3

```

`pyenv local`コマンドを打つと、そのフォルダに`.python-verion`というファイルが作成されて、pythonのバージョンを認識するようになっている.

### virtualenvを使う

```
# 仮想環境の作成
pyenv virtualenv 3.5.3 NAME

# 仮想環境の適用をしたいとき
pyenv local NAME

# 仮想環境に入る
pyenv activate NAME

#　仮想環境から抜ける
pyenv deactivate

# 仮想環境の削除
pyenv uninstall NAME
```

仮想環境にちゃんと入れると、ターミナルで、`(NAME)`のように表示される.

### 環境を保存したいとき

pipでインストールしたパッケージを保存して他の環境で使いたい場合がある.

そのときは、以下のようなコマンドを打てばできる

```
# インストール済みのパッケージをリストに
pip freeze > packages.list

# リストにあるパッケージを一気にインストール
pip install -r packages.list
```

### Anacondaについて

Anacondaは結構有名で、ネット上にもリファレンスがたくさんある.

以前までは、Anacondaをいれていた.

デフォルトで入っているpythonのライブラリの数が多いため、後から自分でインストール作業がなくなる.

なので、もしかしたら、pythonにあまり慣れていない人だったら、anacondaを一発いれてしまえば、ライブラリのインストール作業に追われることなくpythonを扱えるので、そちらのほうがいいのかもしれない.

Anacondaに入っているライブラリは[このページ](https://docs.continuum.io/anaconda/packages/pkg-docs)から確認できるが、とても大量に入っている.

たいていの場合はここまで必要ないと考えたので、anacondaを使うのはやめて、pyenv,virtualenvを使っていくことにした.
