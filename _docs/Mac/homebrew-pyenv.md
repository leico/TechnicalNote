---
layout : post
title  : "homebrew : Python3 をアンインストールして pyenv からインストールし直す "
date   : 2018/08/20
lastchange : 2018-08-21 00:22:23.
tags   :
  - homebrew
  - pyenv
  - python3
---

## Python2 が別環境で必要になった

しかし 2 と 3 が同居しているのもどうかと思ったので `pyenv` を入れることにした。

参考:
* [pipでインストールしたパッケージの場所を調べる - Qiita](https://qiita.com/t-fuku/items/83c721ed7107ffe5d8ff)
* [pipでインストールしたモジュールを全て削除 - Qiita](https://qiita.com/hunzy/items/6965dce22cedb046af7c)
* [Homebrew使い方まとめ - Qiita](https://qiita.com/vintersnow/items/fca0be79cdc28bd2f5e4)
* [MacにpyenvをインストールしてPython環境を構築する \| karakaram-blog](https://www.karakaram.com/mac-pyenv-install)


## Python3 と pip のアンインストール


まずは `pip` でインストールしたモジュールを削除する。どこにインストールされているか調べて、
全てを消去する。


{% capture text %}

## コマンド

```sh
pip show [パッケージ名]
```

例として、requestsを見てみる。


## 出力結果

```sh
Name: requests
Version: 2.18.4
Summary: Python HTTP for Humans.
Home-page: http://python-requests.org
Author: Kenneth Reitz
Author-email: me@kennethreitz.org
License: Apache 2.0
Location: /Users/t-fuku/.pyenv/versions/3.5.1/lib/python3.5/site-packages
Requires: certifi, urllib3, chardet, idna
```

Locationからわかる。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[pipでインストールしたパッケージの場所を調べる - Qiita](https://qiita.com/t-fuku/items/83c721ed7107ffe5d8ff)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
現在入ってるモジュールをfreezeで書きだし、テキストファイルに保存
それをuninstallで読み込む

```sh
pip freeze > piplist.txt
sudo pip uninstall -r piplist.txt
```

何故かパーミッションでエラーが出たので、sudoも付けておいた

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[pipでインストールしたモジュールを全て削除 - Qiita](https://qiita.com/hunzy/items/6965dce22cedb046af7c)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


```sh
$ pip3 show neovim
Name: neovim
Version: 0.2.6
Summary: Python client to neovim
Home-page: http://github.com/neovim/python-client
Author: Thiago de Arruda
Author-email: tpadilha84@gmail.com
License: Apache
Location: /Applications/Homebrew/lib/python3.7/site-packages
Requires: greenlet, msgpack
Required-by: 
```

ここから _/Applications/Homebrew/lib/python3.7/site-packages_ にインストールされているのがわかる。
コレを覚えておいて、全てのモジュールを削除する。

```sh
$ pip3 freeze > piplist.txt
$ pip3 uninstall -r piplist.txt 
Uninstalling greenlet-0.4.14:
  Would remove:
    /Applications/Homebrew/include/python3.7m/greenlet/greenlet.h
    /Applications/Homebrew/lib/python3.7/site-packages/greenlet-0.4.14.dist-info/*
    /Applications/Homebrew/lib/python3.7/site-packages/greenlet.cpython-37m-darwin.so
Proceed (y/n)? y 
  Successfully uninstalled greenlet-0.4.14
Uninstalling msgpack-0.5.6:
  Would remove:
    /Applications/Homebrew/lib/python3.7/site-packages/msgpack-0.5.6.dist-info/*
    /Applications/Homebrew/lib/python3.7/site-packages/msgpack/*
Proceed (y/n)? y
  Successfully uninstalled msgpack-0.5.6
Uninstalling neovim-0.2.6:
  Would remove:
    /Applications/Homebrew/lib/python3.7/site-packages/neovim-0.2.6.dist-info/*
    /Applications/Homebrew/lib/python3.7/site-packages/neovim/*
Proceed (y/n)? y
  Successfully uninstalled neovim-0.2.6
```

これで python 本体を消すことができる。




{% capture text %}


### アンインストール

```sh
brew uninstall formula
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Homebrew使い方まとめ - Qiita](https://qiita.com/vintersnow/items/fca0be79cdc28bd2f5e4)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

```sh
brew uninstall python3
```


こんな感じで削除が完了するが、 _Homebrew/lib/python3.7_ ディレクトリが残っているので消去する。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/homebrew-pyenv/01_trash.png %}{% endcapture %}
{% capture caption %}

このディレクトリをゴミ箱に移動
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


これで完了。


## `pyenv` をインストールする

{% capture text %}


## Pyenv をインストール

Homebrew で入れていた Python をアンインストールできたら
[GitHub – pyenv/pyenv](https://github.com/pyenv/pyenv#homebrew-on-mac-os-x)
を参考に Pyenv をインストール。

```sh
$ brew install pyenv
```

.bash_profile に環境変数や init コマンドを追加。

```sh
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
```


ターミナルを再起動して動作確認。

```sh
$ pyenv --version
pyenv 1.0.10
```

## Python をインストール

[Python.org](http://www.python.org) あたりで最新版の Python バージョンを調べて、以下コマンドでインストール。

```sh
$ pyenv install 2.7.13
```

インストールした Python をグローバルで使うように設定。

```sh
$ pyenv global 2.7.13
```


変更されたか確認。

```sh
$ pyenv versions
 
  system
* 2.7.13 (set by /Users/karasawa/.pyenv/version)
```

バージョン確認。

```sh
$ python --version
Python 2.7.13
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MacにpyenvをインストールしてPython環境を構築する \| karakaram-blog](https://www.karakaram.com/mac-pyenv-install)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

基本は `rbenv` と同じようだ。インストールをする。

```sh
brew install pyenv
```

_.bash_profile_ に以下を追加する。

```bash
#pyenv command
export PYENV_ROOT=/Applications/Homebrew/opt/Python
eval "$(pyenv init -)"
```

以下の `pyenv` ヘルプを見てわかるように、 `pyenv versions` で利用できるバージョンが一覧で出力される。

```sh
$ pyenv
pyenv 1.2.7
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   commands    List all available pyenv commands
   local       Set or show the local application-specific Python version
   global      Set or show the global Python version
   shell       Set or show the shell-specific Python version
   install     Install a Python version using python-build
   uninstall   Uninstall a specific Python version
   rehash      Rehash pyenv shims (run this after installing executables)
   version     Show the current Python version and its origin
   versions    List all Python versions available to pyenv
   which       Display the full path to an executable
   whence      List all Python versions that contain the given executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/pyenv/pyenv#readme
```

今回は 3.7.0 をインストールする。

```sh
pyenv install 3.7.0
```

しばらく待つとインストール完了する。
ほとんど `rbenv` と使用感は同じ。
