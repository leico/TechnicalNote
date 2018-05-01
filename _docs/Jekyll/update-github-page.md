---
layout : post
title  : github-pages環境のアップデート
date   : 2017/06/01
lastchange : 2018-05-02 00:11:52.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
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



{% capture text %}
### 9. bundle clean で古い gem を削除する

bundle update や Gemfile の変更などで使われなくなった gem は bundle clean で削除出来ます。

さきほどの json の例の後だと次のようになります。

```sh
% bundle clean
Removing json (1.5.5)
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[ツールを使いたいだけの人のための bundler 入門 (例: vagrant + veewee) - Qiita](http://qiita.com/znz/items/5471e5826fde29fa9a80)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






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


{% capture text %}
### 不要になったバージョンのRuby環境を削除する

```sh
$ rbenv uninstall -f 2.0.0-p195
$ rbenv rehash
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[rbenvでrubyのバージョン管理をする - Qiita](http://qiita.com/MasahiroSakoda/items/b66f92df80b375f7c786)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





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

### rbenvのバージョンを上げる

[rbenvをアプリケーションフォルダにインストールする]({{site.github.url}}{% link _docs/Jekyll/rbenv-install.md %})では git を利用したので、
同じく git コマンドを利用しながらアップデートを行う。

{% capture text %}
#### Upgrading with Git

If you've installed rbenv manually using Git, you can upgrade to the latest version by pulling from GitHub:

```sh
$ cd ~/.rbenv
$ git pull
```

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[rbenv/rbenv: Groom your app’s Ruby environment](https://github.com/rbenv/rbenv#upgrading-with-git)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


まずはインストールしたディレクトリまで移動し、最新の状態を取得する。

```sh
cd /Applications/Ruby/rbenv
git pull
```

最新版がちゃんとコンパイル通るか、安定して動作するかはわからないので、安定版 or リリース版のソースコードに変更する。
今回の場合は **v1.1.1** リリースを利用する。

安定版を使用するだけなので、ブランチを切らずにただ安定版のソースをチェックアウトする。

```sh
git checkout v1.1.1
```

アップデートを行ったら、コンパイルしたバイナリを削除して再コンパイルする。

{% capture text %}

Optionally, try to compile dynamic bash extension to speed up rbenv. 
Don't worry if it fails; rbenv will still work normally:

```sh
$ cd ~/.rbenv && src/configure && make -C src
```

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[rbenv/rbenv: Groom your app’s Ruby environment](https://github.com/rbenv/rbenv#upgrading-with-git)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


```sh
make clean -C src
  rm -f *.o ../libexec/*.dylib
src/configure
make -C src
  gcc -fno-common     -c -o realpath.o realpath.c
  gcc -dynamiclib -dynamic -undefined dynamic_lookup   -o ../libexec/rbenv-realpath.dylib realpath.o 
```

### ruby-buildのバージョンも上げる

続いて `ruby-build` のバージョンアップも行う。
_rbenv_ ディレクトリ以下にある _plugins/ruby-build_ ディレクトリに移動、git から最新版をダウンロードする。


```sh
cd plugins/ruby_build
git pull
```

こちらも最新の安定版にするので、リリースを確認する。

```sh
git tag
v20110906
v20110906.1
v20110914
...
v20180224
v20180329
v20180424
```

今回の場合はv20180424が最新のリリースなのでこちらを用いる。

```sh
git checkout v20180424
```

### Ruby2.4.2 と bundlerのインストール

[rbenvコマンドの利用方法]({{site.github.url}}{% link _docs/Jekyll/rbenv-usage.md %})を振り返りつつ、2.4.2 とbundlerのインストールを行う。

が、最初以下のようなエラーが起こったのでそれの対処法を載せておく。

{% capture text %}

```sh
rbenv install 2.4.0
ruby-build: use openssl from homebrew
Downloading ruby-2.4.0.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.0.tar.bz2
Installing ruby-2.4.0...
ruby-build: use readline from homebrew

BUILD FAILED (OS X 10.12.6 using ruby-build 20170726)

Inspect or clean up the working tree at /var/folders/9_/xjrq9lv11hl_82pmlzqh3h0m0000gn/T/ruby-build.20170728015414.21759
Results logged to /var/folders/9_/xjrq9lv11hl_82pmlzqh3h0m0000gn/T/ruby-build.20170728015414.21759.log

Last 10 log lines:
  Referenced from: /usr/local/bin/tar
  Expected in: /usr/lib/libSystem.B.dylib

dyld: Symbol not found: _utimensat
  Referenced from: /usr/local/bin/tar
  Expected in: /usr/lib/libSystem.B.dylib

/usr/local/bin/ruby-build: line 344: 21953 Abort trap: 6           tar $tar_args "$package_filename"
/var/folders/9_/xjrq9lv11hl_82pmlzqh3h0m0000gn/T/ruby-build.20170728015414.21759/ruby-2.4.0 /var/folders/9_/xjrq9lv11hl_82pmlzqh3h0m0000gn/T/ruby-build.20170728015414.21759 ~/Documents/Github
/usr/local/bin/ruby-build: line 563: ./configure: No such file or directory
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Ghost](https://github.com/ghost)
from [rbenv install 2.4.0 fails in Mac OS Sierra 10.12.6 Issue #1119](https://github.com/rbenv/ruby-build/issues/1119)
{% endcapture %}
{% assign source=source | markdownify %}
{% include cite.html text=text source=source %}

同じページに対処法も載っていた。

{% capture text %}
looks like the same workaround for [#992 (comment)](https://github.com/rbenv/ruby-build/issues/992#issuecomment-247342449) works here:

```sh
xcode-select --install
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [dkinzer](https://github.com/dkinzer)
from [rbenv install 2.4.0 fails in Mac OS Sierra 10.12.6 Issue #1119](https://github.com/rbenv/ruby-build/issues/1119)
{% endcapture %}
{% assign source=source | markdownify %}
{% include cite.html text=text source=source %}

どうやらCommand Line Toolsが古かったようだ。

{% capture url %}{{ site.github.url }}{% link _docs/Jekyll/images/update-github-page/01.png %}{% endcapture %}
{% assign caption = '
上記のコマンドを実行すると、Command Line Toolsのインストールを行う旨のダイアログが表示される。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Jekyll/images/update-github-page/02.png %}{% endcapture %}
{% assign caption = '
インストールを行おうとすると、利用規約が表示され、同意すると
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Jekyll/images/update-github-page/03.png %}{% endcapture %}
{% assign caption = '
インストールが行われる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

インストールが終了したらRuby 2.4.2をインストールする。

```sh
rbenv install 2.4.2
Downloading openssl-1.1.0h.tar.gz...
-> https://dqw8nmjcqpjn7.cloudfront.net/5835626cde9e99656585fc7aaa2302a73a7e1340bf8c14fd635a62c66802a517
Installing openssl-1.1.0h...
Installed openssl-1.1.0h to /Applications/Ruby/rbenv/versions/2.4.2

Downloading ruby-2.4.2.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.2.tar.bz2
Installing ruby-2.4.2...
Installed ruby-2.4.2 to /Applications/Ruby/rbenv/versions/2.4.2
```

```
rbenv local 2.4.2
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

`bundle update`を行って2.4.2用にインストールする。

```sh
bundle update
Fetching gem metadata from https://rubygems.org/...........
Fetching gem metadata from https://rubygems.org/.
Resolving dependencies...
Fetching concurrent-ruby 1.0.5
Installing concurrent-ruby 1.0.5
Fetching i18n 0.9.5
Installing i18n 0.9.5
Fetching minitest 5.11.3
Installing minitest 5.11.3
Fetching thread_safe 0.3.6
Installing thread_safe 0.3.6
Fetching tzinfo 1.2.5
Installing tzinfo 1.2.5
Fetching activesupport 4.2.9
Installing activesupport 4.2.9
Fetching public_suffix 2.0.5
Installing public_suffix 2.0.5
Fetching addressable 2.5.2
Installing addressable 2.5.2
Using bundler 1.16.1
Fetching coffee-script-source 1.11.1
Installing coffee-script-source 1.11.1
Fetching execjs 2.7.0
Installing execjs 2.7.0
Fetching coffee-script 2.4.1
Installing coffee-script 2.4.1
Fetching colorator 1.1.0
Installing colorator 1.1.0
Fetching ruby-enum 0.7.2
Installing ruby-enum 0.7.2
Fetching commonmarker 0.17.9
Installing commonmarker 0.17.9 with native extensions
Fetching dnsruby 1.60.2
Installing dnsruby 1.60.2
Fetching eventmachine 1.2.6
Installing eventmachine 1.2.6 with native extensions
Fetching http_parser.rb 0.6.0
Installing http_parser.rb 0.6.0 with native extensions
Fetching em-websocket 0.5.1
Installing em-websocket 0.5.1
Fetching ffi 1.9.23
Installing ffi 1.9.23 with native extensions
Fetching ethon 0.11.0
Installing ethon 0.11.0
Fetching multipart-post 2.0.0
Installing multipart-post 2.0.0
Fetching faraday 0.15.0
Installing faraday 0.15.0
Fetching forwardable-extended 2.6.0
Installing forwardable-extended 2.6.0
Fetching gemoji 3.0.0
Installing gemoji 3.0.0
Fetching sawyer 0.8.1
Installing sawyer 0.8.1
Fetching octokit 4.8.0
Installing octokit 4.8.0
Fetching typhoeus 1.3.0
Installing typhoeus 1.3.0
Fetching github-pages-health-check 1.7.3
Installing github-pages-health-check 1.7.3
Fetching rb-fsevent 0.10.3
Installing rb-fsevent 0.10.3
Fetching rb-inotify 0.9.10
Installing rb-inotify 0.9.10
Fetching sass-listen 4.0.0
Installing sass-listen 4.0.0
Fetching sass 3.5.6
Installing sass 3.5.6
Fetching jekyll-sass-converter 1.5.2
Installing jekyll-sass-converter 1.5.2
Fetching ruby_dep 1.5.0
Installing ruby_dep 1.5.0
Fetching listen 3.1.5
Installing listen 3.1.5
Fetching jekyll-watch 2.0.0
Installing jekyll-watch 2.0.0
Fetching kramdown 1.16.2
Installing kramdown 1.16.2
Fetching liquid 4.0.0
Installing liquid 4.0.0
Fetching mercenary 0.3.6
Installing mercenary 0.3.6
Fetching pathutil 0.16.1
Installing pathutil 0.16.1
Fetching rouge 2.2.1
Installing rouge 2.2.1
Fetching safe_yaml 1.0.4
Installing safe_yaml 1.0.4
Fetching jekyll 3.7.3
Installing jekyll 3.7.3
Fetching jekyll-avatar 0.5.0
Installing jekyll-avatar 0.5.0
Fetching jekyll-coffeescript 1.1.1
Installing jekyll-coffeescript 1.1.1
Fetching jekyll-commonmark 1.2.0
Installing jekyll-commonmark 1.2.0
Fetching jekyll-commonmark-ghpages 0.1.5
Installing jekyll-commonmark-ghpages 0.1.5
Fetching jekyll-default-layout 0.1.4
Installing jekyll-default-layout 0.1.4
Fetching jekyll-feed 0.9.3
Installing jekyll-feed 0.9.3
Fetching jekyll-gist 1.5.0
Installing jekyll-gist 1.5.0
Fetching jekyll-github-metadata 2.9.4
Installing jekyll-github-metadata 2.9.4
Fetching mini_portile2 2.3.0
Installing mini_portile2 2.3.0
Fetching nokogiri 1.8.2
Installing nokogiri 1.8.2 with native extensions
Fetching html-pipeline 2.7.2
Installing html-pipeline 2.7.2
Fetching jekyll-mentions 1.3.0
Installing jekyll-mentions 1.3.0
Fetching jekyll-optional-front-matter 0.3.0
Installing jekyll-optional-front-matter 0.3.0
Fetching jekyll-paginate 1.1.0
Installing jekyll-paginate 1.1.0
Fetching jekyll-readme-index 0.2.0
Installing jekyll-readme-index 0.2.0
Fetching jekyll-redirect-from 0.13.0
Installing jekyll-redirect-from 0.13.0
Fetching jekyll-relative-links 0.5.3
Installing jekyll-relative-links 0.5.3
Fetching rubyzip 1.2.1
Installing rubyzip 1.2.1
Fetching jekyll-remote-theme 0.2.3
Installing jekyll-remote-theme 0.2.3
Fetching jekyll-seo-tag 2.4.0
Installing jekyll-seo-tag 2.4.0
Fetching jekyll-sitemap 1.2.0
Installing jekyll-sitemap 1.2.0
Fetching jekyll-swiss 0.4.0
Installing jekyll-swiss 0.4.0
Fetching jekyll-theme-architect 0.1.1
Installing jekyll-theme-architect 0.1.1
Fetching jekyll-theme-cayman 0.1.1
Installing jekyll-theme-cayman 0.1.1
Fetching jekyll-theme-dinky 0.1.1
Installing jekyll-theme-dinky 0.1.1
Fetching jekyll-theme-hacker 0.1.1
Installing jekyll-theme-hacker 0.1.1
Fetching jekyll-theme-leap-day 0.1.1
Installing jekyll-theme-leap-day 0.1.1
Fetching jekyll-theme-merlot 0.1.1
Installing jekyll-theme-merlot 0.1.1
Fetching jekyll-theme-midnight 0.1.1
Installing jekyll-theme-midnight 0.1.1
Fetching jekyll-theme-minimal 0.1.1
Installing jekyll-theme-minimal 0.1.1
Fetching jekyll-theme-modernist 0.1.1
Installing jekyll-theme-modernist 0.1.1
Fetching jekyll-theme-primer 0.5.3
Installing jekyll-theme-primer 0.5.3
Fetching jekyll-theme-slate 0.1.1
Installing jekyll-theme-slate 0.1.1
Fetching jekyll-theme-tactile 0.1.1
Installing jekyll-theme-tactile 0.1.1
Fetching jekyll-theme-time-machine 0.1.1
Installing jekyll-theme-time-machine 0.1.1
Fetching jekyll-titles-from-headings 0.5.1
Installing jekyll-titles-from-headings 0.5.1
Fetching jemoji 0.9.0
Installing jemoji 0.9.0
Fetching minima 2.4.1
Installing minima 2.4.1
Fetching unicode-display_width 1.3.2
Installing unicode-display_width 1.3.2
Fetching terminal-table 1.8.0
Installing terminal-table 1.8.0
Fetching github-pages 183
Installing github-pages 183
Bundle updated!
Post-install message from dnsruby:
Installing dnsruby...
  For issues and source code: https://github.com/alexdalitz/dnsruby
  For general discussion (please tell us how you use dnsruby): https://groups.google.com/forum/#!forum/dnsruby
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

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
# gem "jekyll", "~> 3.7.3"

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
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.0" if Gem.win_platform?

```

ページ用のgemを更新する

```sh
cd webpage
bundle update
The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
Fetching gem metadata from https://rubygems.org/...........
Fetching gem metadata from https://rubygems.org/.
Resolving dependencies....
Using concurrent-ruby 1.0.5
Using i18n 0.9.5
Using minitest 5.11.3
Using thread_safe 0.3.6
Using tzinfo 1.2.5
Using activesupport 4.2.9
Using public_suffix 2.0.5
Using addressable 2.5.2
Using bundler 1.16.1
Using coffee-script-source 1.11.1
Using execjs 2.7.0
Using coffee-script 2.4.1
Using colorator 1.1.0
Using ruby-enum 0.7.2
Using commonmarker 0.17.9
Using dnsruby 1.60.2
Using eventmachine 1.2.6
Using http_parser.rb 0.6.0
Using em-websocket 0.5.1
Using ffi 1.9.23
Using ethon 0.11.0
Using multipart-post 2.0.0
Using faraday 0.15.0
Using forwardable-extended 2.6.0
Using gemoji 3.0.0
Using sawyer 0.8.1
Using octokit 4.8.0
Using typhoeus 1.3.0
Using github-pages-health-check 1.7.3
Using rb-fsevent 0.10.3
Using rb-inotify 0.9.10
Using sass-listen 4.0.0
Using sass 3.5.6
Using jekyll-sass-converter 1.5.2
Using ruby_dep 1.5.0
Using listen 3.1.5
Using jekyll-watch 2.0.0
Using kramdown 1.16.2
Using liquid 4.0.0
Using mercenary 0.3.6
Using pathutil 0.16.1
Using rouge 2.2.1
Using safe_yaml 1.0.4
Using jekyll 3.7.3
Using jekyll-avatar 0.5.0
Using jekyll-coffeescript 1.1.1
Using jekyll-commonmark 1.2.0
Using jekyll-commonmark-ghpages 0.1.5
Using jekyll-default-layout 0.1.4
Using jekyll-feed 0.9.3
Using jekyll-gist 1.5.0
Using jekyll-github-metadata 2.9.4
Using mini_portile2 2.3.0
Using nokogiri 1.8.2
Using html-pipeline 2.7.2
Using jekyll-mentions 1.3.0
Using jekyll-optional-front-matter 0.3.0
Using jekyll-paginate 1.1.0
Using jekyll-readme-index 0.2.0
Using jekyll-redirect-from 0.13.0
Using jekyll-relative-links 0.5.3
Using rubyzip 1.2.1
Using jekyll-remote-theme 0.2.3
Using jekyll-seo-tag 2.4.0
Using jekyll-sitemap 1.2.0
Using jekyll-swiss 0.4.0
Using jekyll-theme-architect 0.1.1
Using jekyll-theme-cayman 0.1.1
Using jekyll-theme-dinky 0.1.1
Using jekyll-theme-hacker 0.1.1
Using jekyll-theme-leap-day 0.1.1
Using jekyll-theme-merlot 0.1.1
Using jekyll-theme-midnight 0.1.1
Using jekyll-theme-minimal 0.1.1
Using jekyll-theme-modernist 0.1.1
Using jekyll-theme-primer 0.5.3
Using jekyll-theme-slate 0.1.1
Using jekyll-theme-tactile 0.1.1
Using jekyll-theme-time-machine 0.1.1
Using jekyll-titles-from-headings 0.5.1
Using jemoji 0.9.0
Using minima 2.4.1
Using unicode-display_width 1.3.2
Using terminal-table 1.8.0
Using github-pages 183
Bundle updated!
```

一個Windows用のgemがエラーを吐いているが問題ない。
これで全てのアップデートが終了！
