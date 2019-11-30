---
layout : post
title  : bundle update で dlopen が OpenSSL でエラー
date   : 2019/12/01
lastchange : 2019-12-01 03:52:15.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github pages
  - dlopen
  - OpenSSL 1.0.0
---

## 久しぶりにアップデートをしようとしたら

`rbenv exec bundle update` したらOpenSSL 1.0.0 がないというエラーを吐いた。

```sh
$ rbenv exec bundle update
Traceback (most recent call last):
	31: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/bin/bundle:23:in `<main>'
	30: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/bin/bundle:23:in `load'
	29: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/exe/bundle:22:in `<top (required)>'
	28: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/friendly_errors.rb:124:in `with_friendly_errors'
	27: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/exe/bundle:30:in `block in <top (required)>'
	26: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/cli.rb:18:in `start'
	25: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/vendor/thor/lib/thor/base.rb:466:in `start'
	24: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/cli.rb:27:in `dispatch'
	23: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/vendor/thor/lib/thor.rb:387:in `dispatch'
	22: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/vendor/thor/lib/thor/invocation.rb:126:in `invoke_command'
	21: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/vendor/thor/lib/thor/command.rb:27:in `run'
	20: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/cli.rb:280:in `update'
	19: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/settings.rb:144:in `temporary'
	18: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/cli.rb:281:in `block in update'
	17: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/cli/update.rb:61:in `run'
	16: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	15: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	14: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/gems/2.5.0/gems/bundler-2.0.2/lib/bundler/installer.rb:4:in `<top (required)>'
	13: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	12: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	11: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/dependency_installer.rb:4:in `<top (required)>'
	10: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 9: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 8: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/package.rb:44:in `<top (required)>'
	 7: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 6: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 5: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/security.rb:12:in `<top (required)>'
	 4: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 3: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
	 2: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/openssl.rb:13:in `<top (required)>'
	 1: from /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
/Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require': dlopen(/Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/x86_64-darwin18/openssl.bundle, 9): Library not loaded: /Applications/Homebrew/opt/openssl/lib/libssl.1.0.0.dylib (LoadError)
  Referenced from: /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/x86_64-darwin18/openssl.bundle
  Reason: image not found - /Applications/Homebrew/opt/Ruby/versions/2.5.3/lib/ruby/2.5.0/x86_64-darwin18/openssl.bundle
```

これを解決する。

## 結局のところ Ruby の再インストールをした

タイトルの通り。まずは Ruby 2.5.3 をアンインストール

```sh
$ rbenv uninstall 2.5.3
rbenv: remove /Applications/Homebrew/opt/Ruby/versions/2.5.3? [yN] y
```

Ruby 2.5.3 をインストール

```sh
$ rbenv install 2.5.3
Downloading openssl-1.1.1d.tar.gz...
-> https://dqw8nmjcqpjn7.cloudfront.net/1e3a91bc1f9dfce01af26026f856e064eab4c8ee0a8f457b5ae30b40b8b711f2
Installing openssl-1.1.1d...
Installed openssl-1.1.1d to /Applications/Homebrew/opt/Ruby/versions/2.5.3

Downloading ruby-2.5.3.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.3.tar.bz2
Installing ruby-2.5.3...
ruby-build: using readline from homebrew
Installed ruby-2.5.3 to /Applications/Homebrew/opt/Ruby/versions/2.5.3
```

OpenSSL のバージョンが上がっている。これが原因か。 

インストールされたか確認する。 2.5.3 があれば大丈夫。ローカルも再インストール前と同じか確認する。

```sh
$ rbenv local
2.5.3
$ rbenv versions
  system
* 2.5.3 (set by /Users/leico_studio/pro/github-pages/.ruby-version)
```

bundler をインストールする。

```sh
$ rbenv exec gem install bundler
Fetching: bundler-2.0.2.gem (100%)
Successfully installed bundler-2.0.2
Parsing documentation for bundler-2.0.2
Installing ri documentation for bundler-2.0.2
Done installing documentation for bundler after 3 seconds
1 gem installed
```

一度 github page 用にインストールされている gem たちを消去する。

```sh
$ rm Gemfile.lock
$ sudo rm -r vendor/*
```

gem たちのアップデートをかける

```sh
$ rbenv exec bundle update
Fetching gem metadata from https://rubygems.org/...........
Fetching gem metadata from https://rubygems.org/.
Resolving dependencies...
Fetching concurrent-ruby 1.1.5
Installing concurrent-ruby 1.1.5
Fetching i18n 0.9.5
Installing i18n 0.9.5
Fetching minitest 5.13.0
Installing minitest 5.13.0
Fetching thread_safe 0.3.6
Installing thread_safe 0.3.6
Fetching tzinfo 1.2.5
Installing tzinfo 1.2.5
Fetching activesupport 4.2.11.1
Installing activesupport 4.2.11.1
Fetching public_suffix 3.1.1
Installing public_suffix 3.1.1
Fetching addressable 2.7.0
Installing addressable 2.7.0
Using bundler 2.0.2
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
Fetching commonmarker 0.17.13
Installing commonmarker 0.17.13 with native extensions
Fetching dnsruby 1.61.3
Installing dnsruby 1.61.3
Fetching eventmachine 1.2.7
Installing eventmachine 1.2.7 with native extensions
Fetching http_parser.rb 0.6.0
Installing http_parser.rb 0.6.0 with native extensions
Fetching em-websocket 0.5.1
Installing em-websocket 0.5.1
Fetching ffi 1.11.3
Installing ffi 1.11.3 with native extensions
Fetching ethon 0.12.0
Installing ethon 0.12.0
Fetching multipart-post 2.1.1
Installing multipart-post 2.1.1
Fetching faraday 0.17.1
Installing faraday 0.17.1
Fetching forwardable-extended 2.6.0
Installing forwardable-extended 2.6.0
Fetching gemoji 3.0.1
Installing gemoji 3.0.1
Fetching sawyer 0.8.2
Installing sawyer 0.8.2
Fetching octokit 4.14.0
Installing octokit 4.14.0
Fetching typhoeus 1.3.1
Installing typhoeus 1.3.1
Fetching github-pages-health-check 1.16.1
Installing github-pages-health-check 1.16.1
Fetching rb-fsevent 0.10.3
Installing rb-fsevent 0.10.3
Fetching rb-inotify 0.10.0
Installing rb-inotify 0.10.0
Fetching sass-listen 4.0.0
Installing sass-listen 4.0.0
Fetching sass 3.7.4
Installing sass 3.7.4
Fetching jekyll-sass-converter 1.5.2
Installing jekyll-sass-converter 1.5.2
Fetching ruby_dep 1.5.0
Installing ruby_dep 1.5.0
Fetching listen 3.1.5
Installing listen 3.1.5
Fetching jekyll-watch 2.2.1
Installing jekyll-watch 2.2.1
Fetching kramdown 1.17.0
Installing kramdown 1.17.0
Fetching liquid 4.0.0
Installing liquid 4.0.0
Fetching mercenary 0.3.6
Installing mercenary 0.3.6
Fetching pathutil 0.16.2
Installing pathutil 0.16.2
Fetching rouge 3.11.0
Installing rouge 3.11.0
Fetching safe_yaml 1.0.5
Installing safe_yaml 1.0.5
Fetching jekyll 3.8.5
Installing jekyll 3.8.5
Fetching jekyll-avatar 0.6.0
Installing jekyll-avatar 0.6.0
Fetching jekyll-coffeescript 1.1.1
Installing jekyll-coffeescript 1.1.1
Fetching jekyll-commonmark 1.3.1
Installing jekyll-commonmark 1.3.1
Fetching jekyll-commonmark-ghpages 0.1.6
Installing jekyll-commonmark-ghpages 0.1.6
Fetching jekyll-default-layout 0.1.4
Installing jekyll-default-layout 0.1.4
Fetching jekyll-feed 0.11.0
Installing jekyll-feed 0.11.0
Fetching jekyll-gist 1.5.0
Installing jekyll-gist 1.5.0
Fetching jekyll-github-metadata 2.12.1
Installing jekyll-github-metadata 2.12.1
Fetching mini_portile2 2.4.0
Installing mini_portile2 2.4.0
Fetching nokogiri 1.10.5
Installing nokogiri 1.10.5 with native extensions
Fetching html-pipeline 2.12.2
Installing html-pipeline 2.12.2
Fetching jekyll-mentions 1.4.1
Installing jekyll-mentions 1.4.1
Fetching jekyll-optional-front-matter 0.3.0
Installing jekyll-optional-front-matter 0.3.0
Fetching jekyll-paginate 1.1.0
Installing jekyll-paginate 1.1.0
Fetching jekyll-readme-index 0.2.0
Installing jekyll-readme-index 0.2.0
Fetching jekyll-redirect-from 0.14.0
Installing jekyll-redirect-from 0.14.0
Fetching jekyll-relative-links 0.6.0
Installing jekyll-relative-links 0.6.0
Fetching rubyzip 2.0.0
Installing rubyzip 2.0.0
Fetching jekyll-remote-theme 0.4.0
Installing jekyll-remote-theme 0.4.0
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
Fetching jemoji 0.10.2
Installing jemoji 0.10.2
Fetching minima 2.5.0
Installing minima 2.5.0
Fetching unicode-display_width 1.6.0
Installing unicode-display_width 1.6.0
Fetching terminal-table 1.8.0
Installing terminal-table 1.8.0
Fetching github-pages 202
Installing github-pages 202
Bundle updated!
Post-install message from dnsruby:
Installing dnsruby...
  For issues and source code: https://github.com/alexdalitz/dnsruby
  For general discussion (please tell us how you use dnsruby): https://groups.google.com/forum/#!forum/dnsruby
Post-install message from sass:

Ruby Sass has reached end-of-life and should no longer be used.

* If you use Sass as a command-line tool, we recommend using Dart Sass, the new
  primary implementation: https://sass-lang.com/install

* If you use Sass as a plug-in for a Ruby web framework, we recommend using the
  sassc gem: https://github.com/sass/sassc-ruby#readme

* For more details, please refer to the Sass blog:
  https://sass-lang.com/blog/posts/7828841

Post-install message from html-pipeline:
-------------------------------------------------
Thank you for installing html-pipeline!
You must bundle Filter gem dependencies.
See html-pipeline README.md for more details.
https://github.com/jch/html-pipeline#dependencies
-------------------------------------------------
```

元に戻ったらしい。
