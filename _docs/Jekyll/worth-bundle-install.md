---
layout : post
title  : gemを間違えてグローバルにインストールしてしまった場合
date   : 2017/01/18
lastchange : 2017-03-01 16:52:46.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

参考

> byのインストールしたgemをすべて削除したい場合がある。
> 例えば、`bundle install --path`を指定していなかった場合などだ。
> 単純に`gem uninstall`を実行すると、
> 
> ```sh
> $ gem uninstall
> ERROR:  While executing gem ... (Gem::CommandLineError)
>     Please specify at least one gem name (e.g. gem build GEMNAME)
> ```
> 
> というようにgemの名前を指定していないのでエラーが出てしまう。
> じゃぁ、指定してやればいいんだろ！と以下を実行すると、
> 
> ```sh
> $ gem uninstall -axI `gem list --no-versions`
> Successfully uninstalled uglifier-2.5.3
> Successfully uninstalled turbolinks-2.4.0
> ERROR:  While executing gem ... (Gem::InstallError)
>     gem "test-unit" cannot be uninstalled because it is a default gem
> ```
> 
> というようにdefault gemを消そうとしているのでエラーが出てしまう。
> じゃぁ、default gem以外指定してやればいいんだろ！と以下を実行するとすべて削除できる。
> 
> ```sh
> $ gem uninstall -axI `gem list --no-versions | egrep -v 'test-unit|rdoc|psych|minitest|io-console|rake|bigdecimal|json'`
> ```
> 
> * [Rubyのgemを全てアンインストールする](http://changesworlds.com/2014/11/uninstall-all-ruby-of-gem/)

egrepの中身を

```sh
rbenv exec gem uninstall -aIx `gem list --no-version`
```

で

```sh
ERROR:  While executing gem ... (Gem::InstallError)
    gem "test-unit" cannot be uninstalled because it is a default gem
```

__cannot be uninstalled because it is a default gem__と怒られたgemを
`|`で繋いでいくとやがて全てのgemをアンインストールできる。

結果こうなった。

```sh
gem uninstall -axI `gem list --no-versions | egrep -v 'rdoc|psych|json|io-console|bigdecimal'`
```

Bundlerまで消去されてしまうので、その後

```sh
rbenv exec gem install bundler
```

を実行する。
