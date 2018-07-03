---
layout : post
title  : Homebrewを /Applications ディレクトリにインストールする
date   : 2018/07/03
lastchange : 2018-07-03 17:41:05.
tags   :
  - Mac
  - High Sierra
  - Homebrew
  - brew
  - Applications
  - アプリケーション
---

## いろいろ重なってHomebrewを入れようと思った

* High Sierraにした
* 自作キーボードをカスタムするための環境整備
* NeoVimもhomebrewがいいらしい
* rbenvもHomebrewがいいらしい

といろいろ重なったのでHomebrewを入れることにした。
Macでアプリの扱いが簡単な方がいいので、 _/Applications_ ディレクトリに
Homebrewをインストールすることにした。

参考:
* [Homebrew のインストール先を変更する - Qiita](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
* [brew(1) – The missing package manager for macOS — Homebrew Documentation](https://docs.brew.sh/Manpage)




## Homebrewを _/Applications_ ディレクトリにインストールする





{% capture text %}

## Homebrew 本体のインストール

まず、あらかじめ `/opt/homebrew` ディレクトリを作成しておきます。

```sh
cd /opt
sudo mkdir homebrew
sudo chown ${USER}:staff homebrew
```

通常のインストールスクリプトを実行する代わりに、以下を実行します。
このコマンドは、Homebrew ドキュメントの 
[Alternative Installs](https://github.com/Homebrew/brew/blob/master/docs/Installation.md#alternative-installs)
に書かれているものです。

```sh
curl -L https://github.com/Homebrew/homebrew/tarball/master | tar xz --strip 1 -C homebrew
```

インストールそのものはこれだけです。
あとは実行パスの設定さえしてしまえば `brew` コマンドが使えます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Homebrew のインストール先を変更する - Qiita](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これを _/Applications_ ディレクトリに置き換えて行う。

まず _/Applications/Homebrew_ を作成する。

```sh
mkdir /Applications/Homebrew
```

このディレクトリに Homebrew をインストールするので載っているコマンドを少し変更して実行する。

```sh
cd /Applications/Homebrew
curl -L https://github.com/Homebrew/homebrew/tarball/master | tar xz --strip 1 -C .
```

上記の例では _/opt/homebrew_ に、 _/opt_ ディレクトリから展開を行っているので、
ディレクトリの移動等でパスを変えている。


## `$PATH` の設定




{% capture text %}


## 実行パスの設定

`/usr/local/bin` と違ってデフォルトで実行パスが通っていないので、以下のどれかで設定します。

* `/etc/paths` に `/opt/homebrew/bin` を追加。
* `/etc/paths.d/homebrew` というファイルを作って `/opt/homebrew/bin` と記述。
    （ただし、`/etc/paths.d/*` は `/etc/paths` よりも後にパスが追加される点に注意）
* `~/.zshrc` や `~/.bashrc` で `PATH` 環境変数を設定。


## キャッシュパスの設定

キャッシュの参照先が `/usr/local` や `/Library/Caches` になってしまうようなので、
パスを変更します。

`~/.zshrc` や `~/.bashrc` で以下を設定します。

```sh
export HOMEBREW_CACHE=/opt/homebrew/cache
```

この内容は、
[コメント](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41#comment-21e0df472b6c5949fb75)
でご指摘いただいたものです。ありがとうございます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Homebrew のインストール先を変更する - Qiita](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

`HOMEBREW_CACHE`
: If set, instructs Homebrew to use the given directory as the download cache.

Default: `~/Library/Caches/Homebrew`.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[brew(1) – The missing package manager for macOS — Homebrew Documentation](https://docs.brew.sh/Manpage)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



_bash\_profile_ に `$PATH` の設定を加える。



```bash
#XDG base directory specification
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_CACHE_HOME="$HOME/.cache"

#VimR command
export PATH="$PATH:/Applications/VimR.app/Contents/Resources"

#Homebrew command
export PATH="$PATH:/Applications/Homebrew/bin"
export HOMEBREW_CACHE="$XDG_CACHE_HOME/Homebrew"
```

`export PATH="$PATH:/Applications/Homebrew/bin"` 
: `brew` へのパスを通している

`export HOMEBREW_CACHE="$XDG_CACHE_HOME/Homebrew"`
: [dein.vimのキャッシュディレクトリの設定]({{site.github.url}}{% link _docs/VimR/VimR-dein-XDG.md %})
でXDGに対応したディレクトリ構成にしているのでキャッシュディレクトリはこれを利用することにする。


## 動くか確認する

```sh
brew install sl
sl -l
```

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/homebrew-applications/01_SL.png %}{% endcapture %}
{% capture caption %}

動けば問題なし

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

