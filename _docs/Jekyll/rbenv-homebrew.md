---
layout : post
title  : rbenvをHomebrewからインストールする
date   : 2018/07/03
lastchange : 2018-07-03 18:01:46.
tags   :
  - Mac
  - High Sierra
  - Homebrew
  - brew
  - rbenv
---



## Homebrewに移行

Homebrewを _/Applications_ ディレクトリで運用できるようになったので、`rbenv`も Homebrew
で管理する。

参考:

* [Homebrewでrbenvインストール - Qiita](https://qiita.com/kanetai/items/0331c3080de1bc0acd1f)
* [Ruby系のコマンドでits extensions are not built](https://blog.freedom-man.com/ruby-notbuilt-message/)
* [gem (Rubygems) のコマンドまとめ - でんのうにっし](http://d.hatena.ne.jp/m__z/20130905/1378367697)




## rbenvのインストール

{% capture text %}

## インストール

いれてたと思ってたら入ってなかったのでbrew install。

```sh
brew update
brew install rbenv ruby-build
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Homebrewでrbenvインストール - Qiita](https://qiita.com/kanetai/items/0331c3080de1bc0acd1f)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


簡単。

```sh
brew install rbenv
```




## 環境変数を変更する

[rbenvをアプリケーションフォルダにインストールする]({{site.github.url}}{% link _docs/Jekyll/rbenv-install.md %})
 で設定した環境変数をこちら用に変更する。

 _bash\_profile_ は以下のようになった。

```bash
#rbenv command
export RBENV_ROOT=/Applications/Homebrew/opt/Ruby
eval "$(rbenv init -)"
 ```

`export RBENV_ROOT=/Applications/Homebrew/opt/Ruby`
: Rubyのインストール先は _Homebrew/opt_ が最適な気がしたのでこのディレクトリにした。


## `bundler` をインストールする

```sh
gem install bundler
```

簡単。



## github-pages のアップデートをかける

```sh
cd technicalnote
bundle update
```

簡単。と思ったら

```sh
Ignoring eventmachine-1.2.6 because its extensions are not built.  Try: gem pristine eventmachine --version 1.2.6
Ignoring ffi-1.9.23 because its extensions are not built.  Try: gem pristine ffi --version 1.9.23
Ignoring nokogiri-1.8.2 because its extensions are not built.  Try: gem pristine nokogiri --version 1.8.2
```

というエラーが出る。


{% capture text %}

あるときからruby系のコマンドを叩く度に以下のようなメッセージが表示されるようになった

```sh
Ignoring nokogiri-1.6.8 because its extensions are not built.  Try: gem pristine nokogiri --version 1.6.8
Ignoring nokogiri-1.6.8.1 because its extensions are not built.  Try: gem pristine nokogiri --version1.6.8.1
Ignoring bcrypt-3.1.11 because its extensions are not built.  Try: gem pristine bcrypt --version 3.1.11
...
```
	
以下のコマンドを叩けば解消されます。

```sh
gem pristine --all
```



{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Ruby系のコマンドでits extensions are not built](https://blog.freedom-man.com/ruby-notbuilt-message/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}
`gem pristine`
: gemの隠し場所にあるファイルからインストールしたgemを初期状態へ戻す
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[gem (Rubygems) のコマンドまとめ - でんのうにっし](http://d.hatena.ne.jp/m__z/20130905/1378367697)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




しかし `gem pristine --all` をしても `gem pristine nokogiri --version 1.6.8 ....` をしても問題が解決しない。
なので一旦 _vendor/bundle_ 内部を全消去して再インストール。

```sh
rm -rf vendor/bundle/*
```

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
Fetching activesupport 4.2.10
Installing activesupport 4.2.10
Fetching public_suffix 2.0.5
Installing public_suffix 2.0.5
Fetching addressable 2.5.2
Installing addressable 2.5.2
Using bundler 1.16.2
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
Fetching eventmachine 1.2.7
Installing eventmachine 1.2.7 with native extensions
Fetching http_parser.rb 0.6.0
Installing http_parser.rb 0.6.0 with native extensions
Fetching em-websocket 0.5.1
Installing em-websocket 0.5.1
Fetching ffi 1.9.25
Installing ffi 1.9.25 with native extensions
Fetching ethon 0.11.0
Installing ethon 0.11.0
Fetching multipart-post 2.0.0
Installing multipart-post 2.0.0
Fetching faraday 0.15.2
Installing faraday 0.15.2
Fetching forwardable-extended 2.6.0
Installing forwardable-extended 2.6.0
Fetching gemoji 3.0.0
Installing gemoji 3.0.0
Fetching sawyer 0.8.1
Installing sawyer 0.8.1
Fetching octokit 4.9.0
Installing octokit 4.9.0
Fetching typhoeus 1.3.0
Installing typhoeus 1.3.0
Fetching github-pages-health-check 1.8.1
Installing github-pages-health-check 1.8.1
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
Fetching nokogiri 1.8.3
Installing nokogiri 1.8.3 with native extensions
Fetching html-pipeline 2.8.3
Installing html-pipeline 2.8.3
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
Fetching jekyll-remote-theme 0.3.1
Installing jekyll-remote-theme 0.3.1
Fetching jekyll-seo-tag 2.5.0
Installing jekyll-seo-tag 2.5.0
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
Fetching unicode-display_width 1.4.0
Installing unicode-display_width 1.4.0
Fetching terminal-table 1.8.0
Installing terminal-table 1.8.0
Fetching github-pages 187
Installing github-pages 187
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
これで問題なく復元できた。
