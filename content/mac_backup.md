Title: mac環境のbackup
Date: 2017-07-16
Slug: mac_backup
Category: プログラミング
Tags: Mac

# アプリについて
- 基本的にbrew-caskを使ってインストールする

```
# brew-caskのインストール
% brew install caskroom/cask/brew-cask

# たとえばGoogle Chromeをインストールするとき
% brew cask install google-chrome

# アプリを探したいとき
# たとえばfirefoxを探したいとき
% brew cask search firefox
==> Exact Match
firefox
==> Partial Matches
multifirefox
==> Remote Matches
caskroom/versions/firefox-esr            caskroom/versions/firefox-beta

# dmgファイルなどのキャッシュ削除
brew cask cleanup

# アプリ削除
brew cask uninstall firefox
```

# Brew-file

* Brew-fileを使うと、brewやbrew-caskを使ってインストールしたアプリやパッケージをファイルで管理できるようになる
* 一緒にmasというパッケージもインストールすると、BrewfileにApp Storeからダウンロードしたアプリも記録される

```
# brew-fileのインストール
% brew tap rcmdnk/file
% brew install brew-file

# masのインストール
% brew install mas
```
* 初期化

Brewfileに最初に書き込むとき以下のコマンドを打つ.

```
% brew file init
```

すると、~/.brewfile/Brewfileに出力される.

- Brewfileの保存場所を変更したいときは、`.bashrc`や`.zshrc` のようなファイルに、

```
export HOMEBREW_BREWFILE=~/Dropbox

```

のように指定すれば、指定舌場所にファイルが保存される.

- Mas

```
122	# App Store applications
123	appstore 808647808 ActivityTimer (2.0.5)
124	appstore 896624060 Kobito (2.3.8)
125	appstore 748212890 Memory Cleaner (2.4)
126	appstore 497799835 Xcode (8.3.3)
```

masのお陰で、このように、AppStoreからインストールしたアプリケーションも記録される.

* 再構築したいときは、

```
brew file install Brewfile
```

で完了する.

- コマンド

```
# Brewfileを確認する
% brew file cat

# Brewfileを編集する
% brew file edit

# Brewfileを更新
% brew file update && brew  

# brew cask に入っているアプリーケーションを更新
% brew file cask_upgrade -C

# brew や brew caskで新しいアプリケーションをインストールするときに、同時にBrewfileも更新するとき
% brew file brew install <package>
% brew file brew cask install <package>
```

* 上記以外の方法にもBrewfileを自動で更新する方法がある. 以下の分を`.zshrc`等に書くことで、brewコマンドが使われたとき、自動でBrewfileがアップデートされる

```
if [ -f $(brew --prefix)/etc/brew-wrap ];then
  source $(brew --prefix)/etc/brew-wrap
fi
```

# Mackup

* mackupを使うと、アプリーケーションの設定を保存してくれる.

```
# インストール
% brew install mackup

# バックアップ
% mackup backup

# 再構築
% mackup restore
```

# Atomのパッケージのバックアップ

```
apm list -bi > Atomfile
```

のようにファイルを作成し、Dropboxなどに保存

# 参考
- [Brew-fileでApp Storeのアプリも含めてMacを完全自動初期化](https://rcmdnk.com/blog/2016/02/13/computer-mac-homebrew-brew-file/)
