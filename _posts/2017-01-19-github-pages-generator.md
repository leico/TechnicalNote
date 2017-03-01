---
layout : post
title  : Github Pages Gemを、複数のプロジェクトからエイリアスで参照する
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## 複数あるGithub Pages全てにGemをインストールするのは無駄

と思ったので

1. __github-pages__ ディレクトリを作成
1. __github-pages__ ディレクトリに`github-pages` gemをインストール
1. 個々のページを子ディレクトリとして管理する。
1. 子ディレクトリから __github-pages__ の`vendor/bundle`や`.bundle/config`へシンボリックリンクを作成して利用する

という方法で運用しようと思った。

## __github-pages__ ディレクトリの準備

__github-pages__ に移動してからRubyのバージョンをgithubに合わせる

```sh
mkdir github-pages
cd github-pages
rbenv local 2.3.3
```

Gemfileを生成する

```sh
rbenv exec bundle init
```

Gemfileを変更する

```sh
# frozen_string_literal: true
source "https://rubygems.org"

gem 'github-pages'
```

インストールする

```sh
rbenv exec bundle install --path vendor/bundle
```

## jekyllのプロジェクトディレクトリの作成

手順は

1. `jekyll new pagename`でテンプレート作成
1. __github-pages__ ディレクトリのファイルにリンクを貼る
  1. `.ruby-version`へのリンク
  1. `.bundle`へのリンク
  1. `vendor`へのリンク
1. `_config.yml`へ除外項目の追加
  
  ```sh
  exclude:
    - vendor
  ```

となるが、この手順を毎回やるのはめんどう。
ってことでShellScript作った。

```sh
#!/bin/sh

# args.length < 1
if [ $# -lt 1 ]; then
  echo "no jekyll project dir"
  exit 1
fi

arg=$1

if [ -e $arg ]; then
  echo "$arg exist"
  exit 1
fi

# --- create jekyll template ----
bundle exec jekyll new $arg



# --- connect link and add vendor to exclude  ---
cd $arg

ln -sf ../.ruby-version ./.ruby-version
ln -sf ../.bundle ./.bundle
ln -sf ../vendor ./vendor

echo "exclude:"   >> _config.yml
echo "  - vendor" >> _config.yml
```

__github-pages__ に`jekyllsetup.sh`として保存した場合、

```sh
cd github-pages
./jekyllsetup.sh projectname
```

で __github-pages__ ディレクトリ下に`projectname`ディレクトリが作成され、
jekyllテンプレートが展開している。

もちろんリンクも除外項目も追加済み。

