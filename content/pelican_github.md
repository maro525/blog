Title: Pelican+Github Pagesでブログ生成
Date: 2017-07-14
Slug: pelican_github
Category: Python
Tags: pelican, python

##### ※環境
* Mac : Sierra 10.12.6
* Python : 3.5.3

### 準備
* virtualenv作成
* `pip install pelican Markdown`
* `pip install ghp-import` -> 後々、Github Pagesへの公開操作が便利になる
* `pelican-quickstart`
    - いろいろ設定を聞かれる.基本的にはデフォルトで問題なし
    - あとで変更できるので、適当に答えても問題なし

```
> Where do you want to create your new web site? [.] .
> What will be the title of this web site? Roberto Note
> Who will be the author of this web site? De Niro
> What will be the default language of this web site? [en] ja
> Do you want to specify a URL prefix? e.g., http://example.com (Y/n) y
> What is your URL prefix? (see above example; no trailing slash) maro525.github.io
> Do you want to enable article pagination? (Y/n) y
> How many articles pper page do you want? [10] 5
> What is your time zone? [Europe/Paris] Asia/Tokyo
> Do you want to generate a Fabfile/Makefile to automate generation and publishing? (Y/n)
> Do you want an auto-reload & simpleHTTP script to assist with theme and site development? (Y/n) n
> Do you want to upload your website using FTP? (y/N) n
> Do you want to upload your website using SSH? (y/N) n
> Do you want to upload your website using Dropbox? (y/N) n
> Do you want to upload your website using S3? (y/N) n
> Do you want to upload your website using Rackspace Clout Files? (y/N) n
> Do you want to upload your website using GitHub Pages? (y/N) y
> Is this your personal page (username.github.io)? (y/N) y
```

* `Do you want to generate a Fabfile/Makefile to automate generation and publishing?` の質問にyesと答えると、makeコマンドやfabコマンドでHTMLを作成できるようになる.
* content/以下に記事を置く
* ブログじゃないページはcontent/pagesにファイルを置く

### 最初の記事を作ってみる
* 記事は`content/`以下に書く
* `test.md`みたいな適当なファイル名で作成

```
Title: Robert Debut
Date: 2017-08-12
Category: Pelican
Tags: pelican, python
Slug: first-post
Author: Robert
Summary: this is summary

# Robert Debut

- マークダウン
- マークダウン

```

* `make html` コマンドでhtml作成
* `make serve` でlocalhostで表示確認

### ページのテーマを変える
* Githubのリポジトリから、（自分のフォルダにthemes/みたいなフォルダを作って)Git Cloneする
* 設定ファイル `pelicanconf.py`内の任意の場所に`THEME = './pelican-octopress-theme-master'`を書く
* `make html` -> `make serve`で確認
* `pelican-thems`コマンドを使うというてもある.

### Github Pages
* Githubの機能で、HTMLをプッシュすると、webに公開してくれる
* User/Organization Pages と Project Pagesという2種類がある.
* User/Organization Pages は `NAME.github.io`というリポジトリを作り、masterにHTMLをアップロードすると`http://NAME.github.io`というURLで静的サイトを公開できる
* Project Pages は任意のリポジトリに、`gh-pages`というブランチを作ると、`http://NAME.github.io/[リポジトリ名]` というURLで静的サイトを公開できる.

**Project PagesでGithub Pagesにアップロードする方法**

(最初のみ)
1. NAME.github.ioというリポジトリをGithubに作成
2. git init


(Githubに反映する手順)
1. make publish
2. ghp-import output
3. git push origin gh-pages

### コマンド
* `make html` -> Markdownからhtmlに書き出し.初期設定では`output/`以下に内容が出力される. (`pelican content -o output -s pelicanconf.py` と同じ)
* `make serve` -> htmlの確認をする. http://localhost:8000 で表示を確認できる(`cd output && python3 -m pelican.server` と同じ)
* `make publish` -> 公開用にhtmlを吐き出す(？）(`pelican content -o output -s publishconf.py`と同じ)
