---
layout : post
title  : bundlerの利用方法
date   : 2017/01/17
lastchange : 2017-06-07 20:18:27.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## 参考サイト

* [橋本商会 - Ruby書くならBundler使え](http://shokai.org/blog/archives/7262)
* [rbenvおよびbundlerの基本的な使用方法](https://www.qoosky.io/techs/8a0f1d8d9e)
* [MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)
* [Ruby STUDIO - Gemfileについて](http://ruby.studio-kingdom.com/bundler/guides/gemfile)
* [bundle installするときはpathを指定しよう](https://blog.dakatsuka.jp/2010/11/09/bundle-install.html)

## bundlerのインストール

[rbenvコマンドの利用方法]({{site.github.url}}{% link _docs/Jekyll/rbenv-usage.md %})
のラストにも登場しているように、

```sh
gem install bundler
```

で現在動作しているRubyにbundlerがインストールされる。

`rbenv`を強制的に指定するには

```sh
rbenv exec gem install bundler
```

となる。

## bundlerの動作

> ## Bundlerとは
>
> プロジェクト内で使うRubygemsを管理するしくみ。
> プロジェクトの一番上のディレクトリに「Gemfile」というテキストファイルを置き、その中にgemの名前（と必要あればバージョンも）書く
>
> [橋本商会 - Ruby書くならBundler使え](http://shokai.org/blog/archives/7262)

Bundlerはカレントディレクトリ内にある`Gemfile`を読み込み、必要なgemをバージョンを合わせてインストールしてくれるらしい。


> ## bundlerをインストールして使う
> 
> Rubyのプラグイン、Gemは普通に使うとグローバルに入ってしまいます。
> それを防ぐために特定ディレクトリに入れる仕組みがbundlerです。
> 今回はcocoapodsのインストールを例に記載します。
> 
> [MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)

そしてbundlerでインストールするとグローバルではなく、プロジェクト毎に個別のパスにインストールすることができるらしい。

## Gemfileの書き方

実際にGemfileを書いてみる。

### `bundle init`でGemfileのテンプレートを作成する

書き始める前に、プロジェクトのカレントディレクトリで

```sh
bundle init
```

もしくは

```sh
rbenv exec bundle init
```

を実行するとGemfileのテンプレートが作成される。

```sh
# frozen_string_literal: true
source "https://rubygems.org"

# gem "rails"
```

### bundleのgemパッケージ取得先の指定

```
source "https://rubygems.org"
```

`Gemfile`のヘッダ部分にパッケージ取得先を指定する。
複数のsourceを指定することもできる。


### 必要なgemを`Gemfile`に記述する

```sh
gem 'nokogiri'
gem 'rails', '3.0.0.beta3'
gem 'rack',  '>=1.0'
gem 'thin',  '~>1.1'
```

といった具合。詳しくは
[Ruby STUDIO - Gemfileについて](http://ruby.studio-kingdom.com/bundler/guides/gemfile)

## Bundlerを使ったgemのインストール


> ##gemをインストール
>
> ```sh
> % bundle install
> ```
> 
> Gemfile.lockが無い場合（はじめてbundle installした時）
> Gemfile.lockというファイルが生成される
> gemの名前とバージョンが列挙されている
> これをgitにcommitしておくと吉
> Gemfileではゆるめにバージョン指定して、詳細はGemfile.lockに任せた方がいい
> 
> 
> ### gemのインストール元
> 
> ローカルにないgemはrubygemsから最新版が
> ローカルにあるgemは、ローカルにある中で一番バージョン番号が大きいものがインストールされる
> 
> 
> Gemfile.lockがある場合（2回目以降のbundle install時）
> 書いてあるバージョンのgemがインストールされる
> 
> [橋本商会 - Ruby書くならBundler使え](http://shokai.org/blog/archives/7262)

この例では管理者権限で実行しているのでグローバルのRubyにgemがインストールされる。

> Rails3からBundlerが導入されgemの管理がしやすくなりましたが、
> 色々なRailsアプリでほいほいbundle installを実行するとシステムにインストールされるgemが結構カオスになってきます。
> また、Rails2とRails3が同居する開発環境だとかなり面倒くさくなります。精神衛生上あまり宜しくありません。
>
> そこでbundlerにオプションを渡してgemを任意のディレクトリにインストールし、gemをRailsプロジェクト毎に管理することをオススメします。
>
> [bundle installするときはpathを指定しよう](https://blog.dakatsuka.jp/2010/11/09/bundle-install.html)

と、いうことなので、localの任意のパスにインストールする方法がよさそうだ。

### gemをローカルにインストールする

> Rails3の場合、使い方はこんな感じになります。
> 
> まず、プロジェクト用のディレクトリを作成し、Gemfileを記述します。
>
> ```sh
> mkdir newapp
> cd ./newapp
> vim Gemfile
> ```
> 
> Gemfileにはrailsだけ指定します。
> 
> ```sh
> source "http://rubygems.org"
> gem "rails", "3.0.1"
> ```
>
> Gemfileを書き終わったらbundleコマンドを使いRailsをインストールします。ここではvendor/bundlerというディレクトリにgemをインストールします。
>
> ```sh
> bundle install --path vendor/bundler
> ```
>
> [bundle installするときはpathを指定しよう](https://blog.dakatsuka.jp/2010/11/09/bundle-install.html)

```sh
bundle install --path path
```

と記述することでローカルにgemがインストールされるようだ。


> Gemfile に書いた内容をインストールします。
> インストール先のディレクトリとしては
> メジャーな vendor/bundle を指定します。
> 
> ```sh
> $ bundle install --path vendor/bundle
> ```
>
> [MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)

pathの設定場所は`vendor/bundle`が一般的のようだ。
なので、インストールコマンドは

```sh
bundle install --path vendor/bundle
```

もしくは

```sh
rbenv exec bundle install --path vendor/bundle
```

と実行した。

## bundleでインストールしたgemを利用する

> 実際に利用するときは頭に bundle exec をつけて実行します。
> cocoapodの場合は pod ほげほげ で実行するので
> 以下の様な形で利用します。
>
> ```sh
> $ bundle exec pod --version
> 0.39.0
> ```
> 
> [MacにHomeBrew,rbenv,bundlerをインストールする](http://qiita.com/shinkuFencer/items/3679cfd966f6a61ccd1b)

`bundle exec`というコマンドでgemを実行するとローカルインストールされたgemが実行される。

逆に通常どおり`gem`とやってしまうとグローバルにインストールされたgemを参照しにいってしまう。


