---
layout : post
title  : Ruby開発環境についての調査
date   : 2017/01/16
lastchange : 2017-03-04 23:13:07.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## 多種多様なRuby

{{page.date | date: "%Y-%m-%d" }}現在、
[Ruby](https://www.ruby-lang.org/ja/)の最新Ver.は2.4.0、
[Github Pages](https://pages.github.com/versions/)では2.3.3である。

Rubyは常に下位互換がある言語ではなく、また言語、ライブラリ双方とも活発に
開発されているため、使いたいライブラリや機能によってバージョンを変える必要がある。

### Rubyのバージョン管理システム

開発するものに合わせてインストールするRubyのバージョンを変えるのは面倒。
なので、開発環境に合わせて最適なRubyを選択できるように、
Rubyのバージョン管理システムがある。
現在の主流は[`rbenv`](https://github.com/rbenv/rbenv)

### Rubyライブラリマネージメントシステム

Rubyは世界中で膨大なライブラリが公開されているので、
Rubyライブラリ専用のパッケージ管理システムがある。
[RubyGems](https://rubygems.org/)がそれにあたる。

### Rubyライブラリのバージョン管理システム

Rubyでは製作するプログラムによって、
ライブラリのバージョンを変えないといけない場面が残念ながら結構あるようだ。
そこで製作プロジェクト毎に異なるバージョンのライブラリを設定することができる、
ライブラリのバージョン管理システムも存在している。
[Bundler](http://bundler.io/)

## macOS標準のRuby

Rubyが入っていれば、後は`gem install`コマンドでインストールできる。
そしてXCodeをインストールしていればRubyはインストールされており、パスも通っている状態にある。
`ruby` も `gem` も利用できる状態にある。

[gemファイルのインストール場所を調べたりする方法](http://qiita.com/ironsand/items/41aaba896954d56484a4)
を元に標準インストールされているRubyのバージョンとgemのパスなどを調べると

```
$ gem environment
RubyGems Environment:
  - RUBYGEMS VERSION: 2.0.14.1
  - RUBY VERSION: 2.0.0 (2015-12-16 patchlevel 648) [universal.x86_64-darwin16]
  - INSTALLATION DIRECTORY: /Library/Ruby/Gems/2.0.0
  - RUBY EXECUTABLE: /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby
  - EXECUTABLE DIRECTORY: /usr/local/bin
  - RUBYGEMS PLATFORMS:
    - ruby
    - universal-darwin-16
  - GEM PATHS:
     - /Library/Ruby/Gems/2.0.0
     - /Users/user/.gem/ruby/2.0.0
     - /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/gems/2.0.0
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :backtrace => false
     - :bulk_threshold => 1000
  - REMOTE SOURCES:
     - https://rubygems.org/
```

Rubyのバージョン
: 2.0

Gemのインストールパス
: /Library/Ruby/Gems/2.0

だった。github pagesのバージョンより古く、gemインストール先も管理者権限が必要な領域なので、
手動でRubyをインストールする方法をしらべよう。

[rbenvを使ってRubyをインストールする方法]({{site.github.url}}{% link _Jekyll/rbenv-install.md %})

