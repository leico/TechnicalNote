---
layout : post
title  : jekyllの利用方法
date   : 2017/01/18
lastchange : 2017-03-01 16:52:14.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## jekyllの利用方法

参考

* [Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/)
* [Jekyll日本語訳プロジェクト](https://jekyllrb-ja.github.io/)
* [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)
* [Jekyllバージョン3.1の導入とGitHub Pagesを試す](http://www.tbn.co.jp/posts/technology/2016/02/12/jekyll-3.html)
* [Github Pages + Jekyll でハマったこと](http://zdogma.hatenablog.com/entry/2016/08/07/220511)

ほとんどは上記サイトに全て書いてある。

## Jekyllのテンプレートサイトを作る

> 起動から実行まで一瞬です。
> 
> ### Quick-start Instructions
> 
> ```
> $ gem install jekyll
> $ jekyll new my-awesome-site
> $ cd my-awesome-site
> $ jekyll serve
>
> # => http://localhost:4000 を見てください
> ```
>
> [Jekyll日本語訳プロジェクト](https://jekyllrb-ja.github.io/)


とあるように、

```sh
jekyll new newsite
```

もしBundlerを使っている場合

```sh
bundle exec jekyll new newsite
```

で`newsite`ディレクトリに新しいページのテンプレートが展開される。

> 続いて、初期設定を行っていきます。
> 
> ```sh
> $ bundle exec jekyll new -f . # 先にwebsiteフォルダを作成してしまったので、-f オプションを付ける必要があります。
> ```
>
> [Jekyllバージョン3.1の導入とGitHub Pagesを試す](http://www.tbn.co.jp/posts/technology/2016/02/12/jekyll-3.html)

既に作られている／何か入っているディレクトリにページを作る場合

```sh
jekyll new -f newsite
```

もしくは

```sh
bundle exec jekyll new -f newsite
```

で作成することができる。


## Jekyllでページをビルドする

> 1. `jekyll build`コマンドは`_site`ディレクトリ以下に変換ファイルを生成する。
> 
> [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

ということで

```sh
jekyll build
```

もしくは

```sh
bundle exec jekyll build
```

コマンドでディレクトリ内のファイルをHTMLで書かれたWebページにビルドする。
ビルド先はデフォルトでは`./_site`となる。

> ## 問題2: Invalid date でフォーマットエラー
> 
> ### 概要
> 
> またもビルドで落ちる。
> 
> ```sh
> $ bundle exec jekyll serve
> Configuration file: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io/_config.yml
>             Source: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io
>        Destination: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io/_site
>  Incremental build: disabled. Enable with --incremental
>       Generating...
>              ERROR: YOUR SITE COULD NOT BE BUILT:
>                     ------------------------------------
>                     Invalid date '<%= Time.now.strftime('%Y-%m-%d %H:%M:%S %z') %>': Document 'vendor/bundle/ruby/2.3.0/gems/jekyll-3.1.6/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb' does not have a valid date in the YAML front matter.
> ```
> 
> なにか余計なものを見に行って落ちているようで、これはすぐに解決策が浮かんだ。
> 
> ### 解決方法
> 
> `_config.yml` に下記を追記する。
> 
> ```sh
> exclude: [vendor]
> ```
> 
> vendor/bundle 以下に gem を入れている場合、ビルド時にそこを読まないように除外してやる必要がある。
> 除外含めた _config.yml の書き方は下記に詳しい。
> 
> [Github Pages + Jekyll でハマったこと](http://zdogma.hatenablog.com/entry/2016/08/07/220511)

ディレクトリ内にgemが入っているディレクトリがあるとビルドがこけるので、
`_config.yml`に除外設定をしてあげる必要があるが、書き方が変わっていた。

```sh
exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
```

markdown記述でリスト形式になっているというか、markdownのリスト形式でも書けるようになっていた。

## Jekyllのプレビューをする

> では結果をブラウザで確認します。次のようにします。
> 
> ```sh
> % jekyll serve
> 
> Configuration file: none
>             Source: /Users/keyes/Google Drive/playground/jk
>        Destination: /Users/keyes/Google Drive/playground/jk/_site
>       Generating... done.
> [2013-05-16 21:48:12] INFO  WEBrick 1.3.1
> [2013-05-16 21:48:12] INFO  ruby 2.0.0 (2013-02-24) [x86_64-darwin12.2.0]
> [2013-05-16 21:48:12] INFO  WEBrick::HTTPServer#start: pid=11794 port=4000
> ```
> 
> （version1.0で従来の`jekyll --server`から`serve`または`server`サブコマンドを利用するようになりました。）
> 
> 4000番ポートでWEBrickが立ち上がりました。http://localhost:4000を開きます。
> 
> [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

ということで

```sh
jekyll serve
```

もしくは

```sh
bundle exec jekyll serve
```

で確認用にサーバが立ち上がる

> 再度サーバーを起動して変化を見ます。ここでは`--watch`オプションを付けて起動してみます。
> 
> ```sh
> % jekyll serve --watch
> ```
> 
> （version1.0では従来の`jekyll --auto`から`serve`サブコマンドに`–watch`オプションを渡すようになりました。）
> 
> 今度はうまくいきました。`--watch`オブション指定により、変更が直ちに反映されました。ソースも見てみます。
> 
> [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

本来なら`--watch`オプションが必要なようだが、`github-pages`では標準で更新したら即時反映されるようになっていた。

## YAMLヘッダ

> 今度はうまくいきました。Jekyllではこの先頭の`---`で挟まれた領域を
> __YAML Front-Matter__(YAML前付け)といいます。ここにそのドキュメントの書誌事項を書きます。
> JekyllはYAML Front-Matter付きのドキュメントをその変換の対象とし、
> それ以外のドキュメントはそのまま`_site`ディレクトリ以下にコピーするのです。
>
> [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

YAMLヘッダにレイアウト等変換用のページ情報を書き、書かないと変換をせずそのままコピーされる。

このページのヘッダはこのようになっている。

```md
---
layout : post
title  : jekyllの利用方法
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---
```

