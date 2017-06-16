---
layout : post
title  : Github Pagesのデバッグ環境を整える
date   : 2017/01/16
lastchange : 2017-06-15 21:41:05.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## Github Pagesについて

{{ page.date|date: "%Y-%m-%d" }}現在、
[github-pages](https://pages.github.com/)
はmarkdownをhtmlに変換するために、
[jekyll](https://jekyllrb.com/)を利用している。

### 静的サイトジェネレータjekyll

jekyllは[Ruby](https://www.ruby-lang.org/ja/)製の静的サイトジェネレータ。
[markdown](http://daringfireball.net/projects/markdown/)で書かれた内容から
HTMLファイルを生成、これをWebサーバにアップロードすればWebサイトができあがる仕組み。
ただのWebサイトだけではなく、ブログのようなサイトも作る事が可能。

github pagesは`git push`したらこれによってhtml変換されるという仕組み。

静的サイトジェネレータの詳細は
[静的サイトジェネレーター Advent Calendar 2016](http://qiita.com/advent-calendar/2016/static-site-generator)
とか

### その他利用しているプラグイン

jekyllだけではなく、他にもhtmlを生成するために幾つかのRubyプログラムが動いている。
[Github Pages - Dependency versions](https://pages.github.com/versions/)
に利用している[RubyGems](https://rubygems.org/)とそのバージョンがまとめられている。


## Github Pagesのデバッグ環境について

### jekyll他github pagesで利用されているRubyプログラム

[Github Pages - Dependency versions](https://pages.github.com/versions/)
に利用しているRubyプログラムの一覧とそのバージョンが記されているが、多い。

この環境をローカルに、比較的カンタンに整えることができるのが、
[github pages gem](https://github.com/github/pages-gem)になる。

### Github Pages Gem

[jekyll](https://jekyllrb.com/)が[Ruby](https://www.ruby-lang.org/ja/)製なのもあって、
Rubyの開発環境が必要となっている。

GemはRubyのライブラリパッケージ管理システム、[RubyGems](https://rubygems.org/)のパッケージの1つで、
更にUsageを見ると[Bundler](https://bundler.io/)も必要らしい。

## Ruby開発環境の整備

### Ruby自体のインストール

参考

* [Ruby開発環境について]({{site.github.url}}{% link _docs/Jekyll/Ruby-research.md %})
* [rbenvをアプリケーションフォルダにインストールする]({{site.github.url}}{% link _docs/Jekyll/rbenv-install.md %})
* [rbenvコマンドの利用方法]({{site.github.url}}{% link _docs/Jekyll/rbenv-usage.md %})

「[Ruby開発環境について]({{site.github.url}}{% link _docs/Jekyll/Ruby-research.md %})」
より、Ruby開発環境を作るために`rbenv`を利用するのが便利らしい。

`rbenv`の利用方法は「[rbenvコマンドの利用方法]({{site.github.url}}{% link _docs/Jekyll/rbenv-usage.md %})」
にまとめた。

### Bundlerの利用方法

参考

* [Bundlerの利用方法]({{site.github.url}}{% link _docs/Jekyll/bundle-usage.md %})

[Bundlerの利用方法]({{site.github.url}}{% link _docs/Jekyll/bundle-usage.md %})に`rbenv`環境でのインストール方法から利用方法をまとめた。


## ローカルでGithub Pagesと同じ環境を作る

### `rbenv`のインストール

`rbenv`はアプリケーションフォルダにインストールすることにし、その方法は
「[rbenvをアプリケーションフォルダにインストールする]({{site.github.url}}{% link _docs/Jekyll/rbenv-install.md %})」
にまとめた。

### `rbenv`を使ったRubyのインストール

* [rbenvコマンドの利用方法]({{site.github.url}}{% link _docs/Jekyll/rbenv-usage.md %})
* [Github Pages - Dependency versions](https://pages.github.com/versions/)

を参考にして、Github Pagesが利用しているバージョンのRubyをインストールする。

2.3.3で話をすすめる。

```sh
$ rbenv install 2.3.3
$ rbenv rehash
```

### プロジェクトディレクトリでのRubyのバージョンを設定する

プロジェクトページを作成して移動する。

```sh
$ mkdir ~/github-pages && cd ~/github-pages
```

現在選択されているバージョンを確認し、

```sh
$ rbenv versions
* system (set by /Applications/Ruby/rbenv/version)
  2.3.3
```

ローカルのバージョンを2.3.3に変更する。

```sh
$ rbenv local 2.3.3
$ rbenv versions
  system
* 2.3.3 (set by /Users/user/pro/github-pages/test/.ruby-version)
```

同時にRuby 2.3.3にBundlerをインストールする。

[Bundlerの利用方法]({{site.github.url}}{% link _docs/Jekyll/bundle-usage.md %})を参考に

```sh
rbenv exec install bundler
rbenv rehash
```


## github-pages gemを使ったデバッグ

### Bundlerを使ったgithub-pagesのインストール

ダウンロードは
[Github - pages-gem](https://github.com/github/pages-gem)
から

Bundlerでインストールするためにまずプロジェクトのディレクトリを作成して移動する。

```sh
$ mkdir github-pages && cd github-pages
```

Bundlerを初期化する。

```sh
$ rbenv exec bundle init
```

作られたGemfileを編集する。

```sh
# frozen_string_literal: true
source "https://rubygems.org"

gem "github-pages"
```

Gemfileはこんな感じ。

Bundlerを使ってGithub Pagesと同じ環境を整える。
gemのインストールパスは`./vendor/bundle`にした。

```sh
$ rbenv exec bundle install --path vendor/bundle
```

以下ログ

```sh
Fetching gem metadata from https://rubygems.org/.........
Fetching version metadata from https://rubygems.org/..
Fetching dependency metadata from https://rubygems.org/.
Installing i18n 0.7.0
Installing json 1.8.6 with native extensions
Installing minitest 5.10.1
Installing thread_safe 0.3.5
Installing public_suffix 2.0.5
Installing coffee-script-source 1.12.2
Installing execjs 2.7.0
Installing colorator 1.1.0
Installing ffi 1.9.17 with native extensions
Installing multipart-post 2.0.0
Installing forwardable-extended 2.6.0
Installing gemoji 2.1.0
Installing net-dns 0.8.0
Installing sass 3.4.23
Installing rb-fsevent 0.9.8
Installing kramdown 1.11.1
Installing liquid 3.0.6
Installing mercenary 0.3.6
Installing rouge 1.11.1
Installing safe_yaml 1.0.4
Installing mini_portile2 2.1.0
Installing jekyll-paginate 1.1.0
Installing jekyll-swiss 0.4.0
Installing minima 2.0.0
Installing unicode-display_width 1.1.3
Using bundler 1.13.7
Installing tzinfo 1.2.2
Installing addressable 2.5.0
Installing coffee-script 2.4.1
Installing ethon 0.10.1
Installing rb-inotify 0.9.7
Installing faraday 0.11.0
Installing pathutil 0.14.0
Installing jekyll-sass-converter 1.3.0
Installing nokogiri 1.6.8.1 with native extensions
Installing terminal-table 1.7.3
Installing activesupport 4.2.7
Installing jekyll-coffeescript 1.0.1
Installing typhoeus 0.8.0
Installing listen 3.0.6
Installing sawyer 0.8.1
Installing html-pipeline 2.5.0
Installing jekyll-watch 1.5.0
Installing octokit 4.6.2
Installing jekyll 3.3.1
Installing github-pages-health-check 1.3.0
Installing jekyll-gist 1.4.0
Installing jekyll-avatar 0.4.2
Installing jekyll-default-layout 0.1.4
Installing jekyll-feed 0.8.0
Installing jekyll-github-metadata 2.3.0
Installing jekyll-mentions 1.2.0
Installing jekyll-optional-front-matter 0.1.2
Installing jekyll-readme-index 0.0.3
Installing jekyll-redirect-from 0.11.0
Installing jekyll-relative-links 0.2.1
Installing jekyll-seo-tag 2.1.0
Installing jekyll-sitemap 0.12.0
Installing jekyll-theme-architect 0.0.3
Installing jekyll-theme-cayman 0.0.3
Installing jekyll-theme-dinky 0.0.3
Installing jekyll-theme-hacker 0.0.3
Installing jekyll-theme-leap-day 0.0.3
Installing jekyll-theme-merlot 0.0.3
Installing jekyll-theme-midnight 0.0.3
Installing jekyll-theme-minimal 0.0.3
Installing jekyll-theme-modernist 0.0.3
Installing jekyll-theme-primer 0.1.7
Installing jekyll-theme-slate 0.0.3
Installing jekyll-theme-tactile 0.0.3
Installing jekyll-theme-time-machine 0.0.3
Installing jekyll-titles-from-headings 0.1.4
Installing jemoji 0.7.0
Installing github-pages 115
Bundle complete! 1 Gemfile dependency, 74 gems now installed.
Bundled gems are installed into ./vendor/bundle.
Post-install message from html-pipeline:
-------------------------------------------------
Thank you for installing html-pipeline!
You must bundle Filter gem dependencies.
See html-pipeline README.md for more details.
https://github.com/jch/html-pipeline#dependencies
-------------------------------------------------
Post-install message from minima:

----------------------------------------------
Thank you for installing minima 2.0!

Minima 2.0 comes with a breaking change that
renders '<your-site>/css/main.scss' redundant.
That file is now bundled with this gem as
'<minima>/assets/main.scss'.

More Information:
https://github.com/jekyll/minima#customization
----------------------------------------------
```

インストール完了！

### jekyllの使い方

* [jekyllの利用方法]({{site.github.url}}{% link _docs/Jekyll/jekyll-usage.md %})

### jekyllでビルド&デバッグ

{% assign text='

_Note: You are not required to install Jekyll separately.
Once the `github-pages` gem is installed, you can build your site using `jekyll build`,
or preview your site using `jekyll serve`. 
For more information about installing Jekyll locally, please see
[the GitHub Help docs on the matter](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)._

' | markdownify %}
{% assign source='
[Github - github/pages-gem](https://github.com/github/pages-gem)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



ということなので、`github-pages`のデバッグには特別なコマンドがあるわけではなく、Jekyllを走らせるようだ。　

github-pages gemを利用する場合、jekyllとgithub-pages gemが同じプロジェクトディレクトリに同居することになる。
jekyllを実行する/できるディレクトリ=プロジェクトディレクトリになるので、`jekyll new`をカレントディレクトリで実行しなければならない。

{% assign text='
### 解決方法

Github Pages 側の公式ドキュメントに導入方法があったので読んでみる。（最初から読んでおけという話）
[Setting up your GitHub Pages site locally with Jekyll - User Documentation](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)

```sh
bundle exec jekyll new . --force
New jekyll site installed in /Users/octocat/my-site.
```

force オプションが必要だったらしい。
すでに存在するディレクトリに jekyll の初期設定を展開する時には必須なのだそうだ。

>
> If the existing directory isn’t empty, you’ll also have to pass the --force option like so jekyll new . --force.
>
' | markdownify %}
{% assign source='
[Github Pages + Jekyll でハマったこと](http://zdogma.hatenablog.com/entry/2016/08/07/220511)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということなので、以下を実行する。

```sh
$ bundle exec jekyll new -f .
```

{% capture text %}
{% capture text2 %}
## 問題2: Invalid date でフォーマットエラー

### 概要

またもビルドで落ちる。

```sh
$ bundle exec jekyll serve
Configuration file: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io/_config.yml
            Source: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io
       Destination: /Users/tomohiro.zoda/git/github.com/zdogma/zdogma.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
             ERROR: YOUR SITE COULD NOT BE BUILT:
                    ------------------------------------
                    Invalid date '<%= Time.now.strftime('%Y-%m-%d %H:%M:%S %z') %>': Document 'vendor/bundle/ruby/2.3.0/gems/jekyll-3.1.6/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb' does not have a valid date in the YAML front matter.
```

なにか余計なものを見に行って落ちているようで、これはすぐに解決策が浮かんだ。

### 解決方法

`_config.yml` に下記を追記する。

```sh
exclude: [vendor]
```

vendor/bundle 以下に gem を入れている場合、ビルド時にそこを読まないように除外してやる必要がある。
除外含めた _config.yml の書き方は下記に詳しい。
{% endcapture %}
{% assign text2=text2 | markdownify %}
{% assign source='
[Github Pages + Jekyll でハマったこと](http://zdogma.hatenablog.com/entry/2016/08/07/220511)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text2 source=source %}


ディレクトリ内にgemが入っているディレクトリがあるとビルドがこけるので、
`_config.yml`に除外設定をしてあげる必要があるが、書き方が変わっていた。

```sh
exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[jekyllの利用方法]({{site.github.url}}{% link _docs/Jekyll/jekyll-usage.md %})
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



`_config.yml`に以下を追加する。

```sh
exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
```

`.git`や`.ruby-version`等、隠しファイルは最初から除外対象。
これで`jekyll build`や`jekyll serve`が実行できる。
