---
layout : post
title  : github-pages環境のアップデート
date   : 2017/06/01
lastchange : 2017-06-01 20:31:42.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
  - uninstall
  - update
---

## github-pagesに更新がきていた

ので、更新方法をまとめておく。

参考

* [Dependency versions \| GitHub Pages](https://pages.github.com/versions/)
* [rbenvでrubyのバージョン管理をする - Qiita](http://qiita.com/MasahiroSakoda/items/b66f92df80b375f7c786)
* [ツールを使いたいだけの人のための bundler 入門 (例: vagrant + veewee) - Qiita](http://qiita.com/znz/items/5471e5826fde29fa9a80)

## Rubyのバージョンが上がっている場合

Ruby自体のバージョンが上がっていて、そのバージョンを他の開発で使っていない場合は
ローカルにインストールされているGem含めてアンインストールし、余分な容量削減とか事故防止とかをする。

その上で対応しているバージョンのRubyをインストールする。

Gemのアップデートのみの場合は下の方まで飛ばして大丈夫。

### ローカルにインストールしているgemのアンインストール

> ### 9. bundle clean で古い gem を削除する
> 
> bundle update や Gemfile の変更などで使われなくなった gem は bundle clean で削除出来ます。
>
> さきほどの json の例の後だと次のようになります。
> 
> ```sh
> % bundle clean
> Removing json (1.5.5)
> ```
> 
> ---
> 
> * [ツールを使いたいだけの人のための bundler 入門 (例: vagrant + veewee) - Qiita](http://qiita.com/znz/items/5471e5826fde29fa9a80)


`bundle clean`を使えば手軽に削除できそう。
全てのgemが不要になったことを知らせるために **Gemfile** を変更する。

```ruby
# frozen_string_literal: true
source "https://rubygems.org"

#gem 'github-pages'
#gem 'jekyll-gist'
```

全てのgemをコメントアウト。そして`bundle clean`を実行する。

```sh
$ bundle clean
Removing activesupport (4.2.8)
Removing addressable (2.5.1)
Removing coffee-script (2.4.1)
Removing coffee-script-source (1.12.2)
Removing colorator (1.1.0)
Removing ethon (0.10.1)
Removing execjs (2.7.0)
Removing faraday (0.12.1)
Removing ffi (1.9.18)
Removing forwardable-extended (2.6.0)
Removing gemoji (3.0.0)
Removing github-pages (139)
Removing github-pages-health-check (1.3.3)
Removing html-pipeline (2.6.0)
Removing i18n (0.8.4)
Removing jekyll (3.4.3)
Removing jekyll-avatar (0.4.2)
Removing jekyll-coffeescript (1.0.1)
Removing jekyll-default-layout (0.1.4)
Removing jekyll-feed (0.9.2)
Removing jekyll-gist (1.4.0)
Removing jekyll-github-metadata (2.3.1)
Removing jekyll-mentions (1.2.0)
Removing jekyll-optional-front-matter (0.1.2)
Removing jekyll-paginate (1.1.0)
Removing jekyll-readme-index (0.1.0)
Removing jekyll-redirect-from (0.12.1)
Removing jekyll-relative-links (0.4.0)
Removing jekyll-sass-converter (1.5.0)
Removing jekyll-seo-tag (2.2.3)
Removing jekyll-sitemap (1.0.0)
Removing jekyll-swiss (0.4.0)
Removing jekyll-theme-architect (0.0.4)
Removing jekyll-theme-cayman (0.0.4)
Removing jekyll-theme-dinky (0.0.4)
Removing jekyll-theme-hacker (0.0.4)
Removing jekyll-theme-leap-day (0.0.4)
Removing jekyll-theme-merlot (0.0.4)
Removing jekyll-theme-midnight (0.0.4)
Removing jekyll-theme-minimal (0.0.4)
Removing jekyll-theme-modernist (0.0.4)
Removing jekyll-theme-primer (0.2.1)
Removing jekyll-theme-slate (0.0.4)
Removing jekyll-theme-tactile (0.0.4)
Removing jekyll-theme-time-machine (0.0.4)
Removing jekyll-titles-from-headings (0.1.5)
Removing jekyll-watch (1.5.0)
Removing jemoji (0.8.0)
Removing kramdown (1.13.2)
Removing liquid (3.0.6)
Removing listen (3.0.6)
Removing mercenary (0.3.6)
Removing mini_portile2 (2.1.0)
Removing minima (2.1.1)
Removing minitest (5.10.2)
Removing multipart-post (2.0.0)
Removing net-dns (0.8.0)
Removing nokogiri (1.7.2)
Removing octokit (4.7.0)
Removing pathutil (0.14.0)
Removing public_suffix (2.0.5)
Removing rb-fsevent (0.9.8)
Removing rb-inotify (0.9.8)
Removing rouge (1.11.1)
Removing safe_yaml (1.0.4)
Removing sass (3.4.24)
Removing sawyer (0.8.1)
Removing terminal-table (1.8.0)
Removing thread_safe (0.3.6)
Removing typhoeus (0.8.0)
Removing tzinfo (1.2.3)
Removing unicode-display_width (1.2.1)
```

きれいに消えたみたい。

しかし、 **vendor/bundle/ruby/2.3.0/** 以下にまだディレクトリが残っているので

```sh
rm -rf vendor/bundle/ruby/2.3.0
```

これでローカルのRuby環境はきれいになった。


### 不要になったRubyのアンインストール


> ### 不要になったバージョンのRuby環境を削除する
> 
> $ rbenv uninstall -f 2.0.0-p195
> $ rbenv rehash
> 
> ---
> 
> * [rbenvでrubyのバージョン管理をする - Qiita](http://qiita.com/MasahiroSakoda/items/b66f92df80b375f7c786)

今回は2.3.3をアンインストールするので

```sh
rbenv uninstall -f 2.3.3
rbenv rehash
```

を行う。

```sh
rbenv uninstall -f 2.3.3
rbenv rehash
rbenv versions
rbenv: version `2.3.3' is not installed (set by /Users/leico_studio/pro/github-pages/github-pages/.ruby-version)
  system
  2.4.0
```

ローカルの設定が **2.3.3** なので怒られている。

### Ruby2.4.0とbundlerのインストール

[rbenvコマンドの利用方法]({{site.github.url}}{% link _Jekyll/rbenv-usage.md %})を振り返りつつ、2.4.0とbundlerのインストールを行う。

```sh
rbenv install 2.4.0

Downloading openssl-1.0.2j.tar.gz...
-> https://dqw8nmjcqpjn7.cloudfront.net/e7aff292be21c259c6af26469c7a9b3ba26e9abaaffd325e3dccc9785256c431
Installing openssl-1.0.2j...
Installed openssl-1.0.2j to /Applications/Ruby/rbenv/versions/2.4.0

Downloading ruby-2.4.0.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.0.tar.bz2
Installing ruby-2.4.0...
Installed ruby-2.4.0 to /Applications/Ruby/rbenv/versions/2.4.0

rbenv rehash
```

```
rbenv local 2.4.0
gem install bundler
```

## Gemのアップデートをする

Rubyのバージョンアップがあった場合、先ほどコメントアウトした **Gemfile** からコメントを外す。

```ruby
# frozen_string_literal: true
source "https://rubygems.org"

gem 'github-pages'
gem 'jekyll-gist'
```

### github-pagesのgemをインストール

`bundle update`を行って2.4.0用にインストールする。

```sh
bundle update
Fetching gem metadata from https://rubygems.org/............
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies...
Fetching i18n 0.8.4
Installing i18n 0.8.4
Fetching minitest 5.10.2
Installing minitest 5.10.2
Fetching thread_safe 0.3.6
Installing thread_safe 0.3.6
Fetching public_suffix 2.0.5
Installing public_suffix 2.0.5
Fetching net-dns 0.8.0
Installing net-dns 0.8.0
Fetching multipart-post 2.0.0
Installing multipart-post 2.0.0
Fetching ffi 1.9.18
Installing ffi 1.9.18 with native extensions
Fetching colorator 1.1.0
Installing colorator 1.1.0
Fetching sass 3.4.24
Installing sass 3.4.24
Fetching rb-fsevent 0.9.8
Installing rb-fsevent 0.9.8
Fetching kramdown 1.13.2
Installing kramdown 1.13.2
Fetching liquid 3.0.6
Installing liquid 3.0.6
Fetching mercenary 0.3.6
Installing mercenary 0.3.6
Fetching forwardable-extended 2.6.0
Installing forwardable-extended 2.6.0
Fetching rouge 1.11.1
Installing rouge 1.11.1
Fetching safe_yaml 1.0.4
Installing safe_yaml 1.0.4
Fetching coffee-script-source 1.12.2
Installing coffee-script-source 1.12.2
Fetching execjs 2.7.0
Installing execjs 2.7.0
Fetching mini_portile2 2.1.0
Installing mini_portile2 2.1.0
Fetching jekyll-paginate 1.1.0
Installing jekyll-paginate 1.1.0
Fetching jekyll-swiss 0.4.0
Installing jekyll-swiss 0.4.0
Fetching gemoji 3.0.0
Installing gemoji 3.0.0
Fetching unicode-display_width 1.2.1
Installing unicode-display_width 1.2.1
Using bundler 1.15.0
Fetching tzinfo 1.2.3
Installing tzinfo 1.2.3
Fetching addressable 2.5.1
Installing addressable 2.5.1
Fetching faraday 0.12.1
Installing faraday 0.12.1
Fetching ethon 0.10.1
Installing ethon 0.10.1
Fetching rb-inotify 0.9.8
Installing rb-inotify 0.9.8
Fetching jekyll-sass-converter 1.5.0
Installing jekyll-sass-converter 1.5.0
Fetching pathutil 0.14.0
Installing pathutil 0.14.0
Fetching coffee-script 2.4.1
Installing coffee-script 2.4.1
Fetching nokogiri 1.7.2
Installing nokogiri 1.7.2 with native extensions
Fetching terminal-table 1.8.0
Installing terminal-table 1.8.0
Fetching activesupport 4.2.8
Installing activesupport 4.2.8
Fetching sawyer 0.8.1
Installing sawyer 0.8.1
Fetching typhoeus 0.8.0
Installing typhoeus 0.8.0
Fetching listen 3.0.6
Installing listen 3.0.6
Fetching jekyll-coffeescript 1.0.1
Installing jekyll-coffeescript 1.0.1
Fetching html-pipeline 2.6.0
Installing html-pipeline 2.6.0
Fetching octokit 4.7.0
Installing octokit 4.7.0
Fetching jekyll-watch 1.5.0
Installing jekyll-watch 1.5.0
Fetching github-pages-health-check 1.3.3
Installing github-pages-health-check 1.3.3
Fetching jekyll-gist 1.4.0
Installing jekyll-gist 1.4.0
Fetching jekyll 3.4.3
Installing jekyll 3.4.3
Fetching jekyll-avatar 0.4.2
Installing jekyll-avatar 0.4.2
Fetching jekyll-default-layout 0.1.4
Installing jekyll-default-layout 0.1.4
Fetching jekyll-feed 0.9.2
Installing jekyll-feed 0.9.2
Fetching jekyll-github-metadata 2.3.1
Installing jekyll-github-metadata 2.3.1
Fetching jekyll-mentions 1.2.0
Installing jekyll-mentions 1.2.0
Fetching jekyll-optional-front-matter 0.1.2
Installing jekyll-optional-front-matter 0.1.2
Fetching jekyll-readme-index 0.1.0
Installing jekyll-readme-index 0.1.0
Fetching jekyll-redirect-from 0.12.1
Installing jekyll-redirect-from 0.12.1
Fetching jekyll-relative-links 0.4.0
Installing jekyll-relative-links 0.4.0
Fetching jekyll-seo-tag 2.2.3
Installing jekyll-seo-tag 2.2.3
Fetching jekyll-sitemap 1.0.0
Installing jekyll-sitemap 1.0.0
Fetching jekyll-theme-architect 0.0.4
Installing jekyll-theme-architect 0.0.4
Fetching jekyll-theme-cayman 0.0.4
Installing jekyll-theme-cayman 0.0.4
Fetching jekyll-theme-dinky 0.0.4
Installing jekyll-theme-dinky 0.0.4
Fetching jekyll-theme-hacker 0.0.4
Installing jekyll-theme-hacker 0.0.4
Fetching jekyll-theme-leap-day 0.0.4
Installing jekyll-theme-leap-day 0.0.4
Fetching jekyll-theme-merlot 0.0.4
Installing jekyll-theme-merlot 0.0.4
Fetching jekyll-theme-midnight 0.0.4
Installing jekyll-theme-midnight 0.0.4
Fetching jekyll-theme-minimal 0.0.4
Installing jekyll-theme-minimal 0.0.4
Fetching jekyll-theme-modernist 0.0.4
Installing jekyll-theme-modernist 0.0.4
Fetching jekyll-theme-primer 0.2.1
Installing jekyll-theme-primer 0.2.1
Fetching jekyll-theme-slate 0.0.4
Installing jekyll-theme-slate 0.0.4
Fetching jekyll-theme-tactile 0.0.4
Installing jekyll-theme-tactile 0.0.4
Fetching jekyll-theme-time-machine 0.0.4
Installing jekyll-theme-time-machine 0.0.4
Fetching jekyll-titles-from-headings 0.1.5
Installing jekyll-titles-from-headings 0.1.5
Fetching jemoji 0.8.0
Installing jemoji 0.8.0
Fetching minima 2.1.1
Installing minima 2.1.1
Fetching github-pages 139
Installing github-pages 139
Bundle updated!
Post-install message from html-pipeline:
-------------------------------------------------
Thank you for installing html-pipeline!
You must bundle Filter gem dependencies.
See html-pipeline README.md for more details.
https://github.com/jch/html-pipeline#dependencies
-------------------------------------------------
```

これで最新版のgithub-pagesが利用できる。

### jekyllテンプレートを出力させる

jekyllページディレクトリの **Gemfile** も変更されているのでテンプレートを出力させてコピーする。

```sh
rm webpage/Gemfile
bundle exec jekyll new test
cp test/Gemfile webpage/.
```

コピーした **Gemfile** をgithub-pages用に編集する。

```ruby
source "https://rubygems.org"
ruby RUBY_VERSION

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
# gem "jekyll", "3.4.3"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.0"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

ページ用のgemを更新する

```sh
cd webpage
bundle update
The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
Fetching gem metadata from https://rubygems.org/............
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies...
Using public_suffix 2.0.5
Using colorator 1.1.0
Using sass 3.4.24
Using rb-fsevent 0.9.8
Using ffi 1.9.18
Using kramdown 1.13.2
Using liquid 3.0.6
Using mercenary 0.3.6
Using forwardable-extended 2.6.0
Using rouge 1.11.1
Using safe_yaml 1.0.4
Using i18n 0.8.4
Using minitest 5.10.2
Using thread_safe 0.3.6
Using net-dns 0.8.0
Using multipart-post 2.0.0
Using coffee-script-source 1.12.2
Using execjs 2.7.0
Using mini_portile2 2.1.0
Using jekyll-paginate 1.1.0
Using jekyll-swiss 0.4.0
Using gemoji 3.0.0
Using unicode-display_width 1.2.1
Using bundler 1.15.0
Using addressable 2.5.1
Using jekyll-sass-converter 1.5.0
Using rb-inotify 0.9.8
Using ethon 0.10.1
Using pathutil 0.14.0
Using tzinfo 1.2.3
Using faraday 0.12.1
Using coffee-script 2.4.1
Using nokogiri 1.7.2
Using terminal-table 1.8.0
Using listen 3.0.6 (was 3.0.8)
Using typhoeus 0.8.0
Using activesupport 4.2.8
Using sawyer 0.8.1
Using jekyll-coffeescript 1.0.1
Using jekyll-watch 1.5.0
Using html-pipeline 2.6.0
Using octokit 4.7.0
Using jekyll 3.4.3
Using github-pages-health-check 1.3.3
Using jekyll-gist 1.4.0
Using minima 2.1.1
Using jekyll-avatar 0.4.2
Using jekyll-default-layout 0.1.4
Using jekyll-feed 0.9.2
Using jekyll-github-metadata 2.3.1
Using jekyll-mentions 1.2.0
Using jekyll-optional-front-matter 0.1.2
Using jekyll-readme-index 0.1.0
Using jekyll-redirect-from 0.12.1
Using jekyll-relative-links 0.4.0
Using jekyll-seo-tag 2.2.3
Using jekyll-sitemap 1.0.0
Using jekyll-theme-architect 0.0.4
Using jekyll-theme-cayman 0.0.4
Using jekyll-theme-dinky 0.0.4
Using jekyll-theme-hacker 0.0.4
Using jekyll-theme-leap-day 0.0.4
Using jekyll-theme-merlot 0.0.4
Using jekyll-theme-midnight 0.0.4
Using jekyll-theme-minimal 0.0.4
Using jekyll-theme-modernist 0.0.4
Using jekyll-theme-primer 0.2.1
Using jekyll-theme-slate 0.0.4
Using jekyll-theme-tactile 0.0.4
Using jekyll-theme-time-machine 0.0.4
Using jekyll-titles-from-headings 0.1.5
Using jemoji 0.8.0
Using github-pages 139
Bundle updated!
```

一個Windows用のgemがエラーを吐いているが問題ない。
これで全てのアップデートが終了！
