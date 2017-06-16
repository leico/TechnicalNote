---
layout : post
title  : rbenvをアプリケーションフォルダにインストールする
date   : 2017/01/16
lastchange : 2017-06-16 23:07:20.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## Rubyのインストール方法

* [Ruby - ダウンロード](https://www.ruby-lang.org/ja/downloads/)
* [MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)
* [rbenv を利用した Ruby 環境の構築](http://dev.classmethod.jp/server-side/language/build-ruby-environment-by-rbenv/)

公式ページを含め、結構な量のWebサイトが`rbenv`を使うことを推奨している。
Rubyのバージョン管理なので当然といえば当然なのだが、プロジェクト毎に違うバージョンのRubyが利用できるようになるようだ。

## `rbenv`をインストールする方法

[MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)
のように、[HomeBrew](http://brew.sh/index_ja.html)で自家醸造するのが一般的らしい。
しかし、HomeBrewは


{% capture text %}
Homebrew は /usr/local にインストールすることを推奨しています。

しかし、/usr/local はそもそもホスト管理者に与えられた場所で、
自前でビルドしたものやローカルで用意したツールなどを置く場所です。パッケージマネージャに入ってきてほしい場所ではありません。

/usr/local を本来の意味で使いたいため、Homebrew は /opt にインストールすることにしました。
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Homebrew のインストール先を変更する](http://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





というように`/usr/local`を利用するのでよろしくない。
そしてmacOSを使っているのに`/opt/homebrew`にインストールするというのもなんだか・・・という感じ。

[`rbenv`公式リポジトリ](https://github.com/rbenv/rbenv)を見てみると、

> ```
> $ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
> ```

と、ホームディレクトリに`.rbenv`隠しディレクトリを作りインストールする方法が載っていた。
`/usr/local`や`/opt`などにインストールするのも気持ち悪いが、ホームディレクトリ内に隠しファイルでインストールされるのも気持ち悪い。

と悩んでいたところ、macOSらしく`/Applications`にインストールする方法を思いつく。

## `rbenv`をアプリケーションフォルダにインストールする

大変参考になったサイト
: [rbenv + ruby-build はどうやって動いているのか](http://takatoshiono.hatenablog.com/entry/2015/01/09/012040)

ここに書かれている`RBENV_ROOT`の設定をしないと、
存在しない`~/.rbenv`にパスが通っているため、`ruby-build`が動作せず、Rubyのインストールもされない。

インストール手順は

1. アプリケーションフォルダに`Ruby`ディレクトリ作成
1. `Ruby/rbenv`に`git clone`する
1. `rbenv`インストール後、`ruby-build`プラグインをインストールする
1. 環境変数などを`~/.bash_profle`に設定する

となる。

これらをまとめたシェルスクリプトを記述した。

```sh
#!/bin/sh

rubydir="/Applications/Ruby"

# create install directory
if [ ! -d $rubydir ]; then
  mkdir $rubydir
fi

rbenvdir="$rubydir/rbenv"

# not exist rbenv or non file in rbenv
if [ \( ! -d $rbenvdir \) -o \( -z "`ls -A $rbenvdir`" \) ]; then

  # clone rbenv
  git clone https://github.com/rbenv/rbenv.git $rbenvdir

  # make rbenv
  cd $rbenvdir
  ls -a
  ./src/configure
  make -C src

  #Add PATH
  echo "\n" >> ~/.bash_profile
  echo "#rbenv command" >> ~/.bash_profile
  echo "export PATH=\"\$PATH:/Applications/Ruby/rbenv/bin\"" >> ~/.bash_profile
  echo "export RBENV_ROOT=$rbenvdir" >> ~/.bash_profile
  echo "eval \"\$(rbenv init -)\"" >> ~/.bash_profile

  #ruby-build install
  git clone https://github.com/rbenv/ruby-build.git $rbenvdir/plugins/ruby-build

  # init
  bin/rbenv init

  echo "need restart terminal!"
fi
```

このシェルスクリプトを実行すれば`rbenv`が`/Applications/Ruby`にインストールされ、
rbenvコマンドが実行可能になる。
