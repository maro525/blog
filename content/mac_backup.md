Title: mac環境のbackup
Date: 2017-07-16
Slug: mac_backup
Category: ノート
Tags: Mac

#### brew file

- ` brew file init` で `~/.brewfile/Brewfile` に記録される
- `export HOMEBREW_BREWFILE=~/Dropbox/Brewfile` でBrewfileの場所を変更し、Dropboxと自動で同期
- `brew install mas`をしておくと、App Storeからインストールしたアプリケーションも記録される

#### dotfiles
- ドットファイルを ` ~/dotfiles` 以下に保存し
- 元の場所へシンボリックリンク `ln -sf ~/dotfiles/.bashrc ~/.bashrc`
- Githubにあげる
- 他の環境で同期するときはgit pull

#### kobitoデータのバックアップ
- `~/Dropbox/kobito` の中に保存
- `~/Libarary/Containers/com.qiita.Kobito/Data/Library/Kobito` 以下にハードリンク
