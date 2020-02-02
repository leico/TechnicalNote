---
layout : post
title  : "Homebrew : nodenv を用いて Node.js の環境構築を行う"
date   : 2020/02/03
lastchange : 2020-02-03 01:24:23.
tags   :
  - Mac
  - Homebrew
  - brew
  - nodenv
---

## Node.js を利用する時がきた

しかし `ndenv` の記事が多い。 `ndenv` には別途 `node-build` のインストールが必要という記述もみられてなんかスマートじゃない。
さらに `ndenv` は時代遅れという記述もある。





{% capture text %}
## [Deprecated] nodenv is better alternative

Please consider to use [nodenv](https://github.com/nodenv/nodenv). ndenv repository is not maintained actively.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[riywo/ndenv: node.js version manager based on rbenv](https://github.com/riywo/ndenv)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





最近はどうやら `nodenv` というものがあるらしい。`node-build` は自動で入るのかな。とりあえずやってみた記録を残す。

参考:

* [riywo/ndenv: node.js version manager based on rbenv](https://github.com/riywo/ndenv)
* [HomebrewでmacOSにnodenvを導入する（2018年版） - えんぴつぶろぐ](https://empitsu88.hatenablog.com/entry/2018/11/01/000000)
* [nodenv/nodenv: Manage multiple NodeJS versions.](https://github.com/nodenv/nodenv#nodenv-shell)
* [Node.js入門](http://www.tohoho-web.com/ex/nodejs.html)
* [イマドキのJavaScriptの書き方2018 - Qiita](https://qiita.com/shibukawa/items/19ab5c381bbb2e09d0d9)



## homebrew から `nodenv` のインストール

{% capture text %}
Homebrew経由でnodenvをインストールします。

```sh
$brew install nodenv
```

正常にnodenvがインストールされたか確認します。

```sh
$nodenv -v
nodenv 1.1.2 # ok
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HomebrewでmacOSにnodenvを導入する（2018年版） - えんぴつぶろぐ](https://empitsu88.hatenablog.com/entry/2018/11/01/000000)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


とりあえずこれだけで入るには入るようだ。やってみる。

```sh
$ brew install nodenv
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
gnutls                                   libtasn1

==> Installing dependencies for nodenv: node-build
==> Installing nodenv dependency: node-build
==> Downloading https://github.com/nodenv/node-build/archive/v4.7.0.tar.gz
==> Downloading from https://codeload.github.com/nodenv/node-build/tar.gz/v4.7.0
######################################################################## 100.0%
==> ./install.sh
🍺  /Applications/Homebrew/Cellar/node-build/4.7.0: 600 files, 684.2KB, built in 10 seconds
==> Installing nodenv
==> Downloading https://github.com/nodenv/nodenv/archive/v1.3.1.tar.gz
==> Downloading from https://codeload.github.com/nodenv/nodenv/tar.gz/v1.3.1
######################################################################## 100.0%
==> src/configure
==> make -C src
🍺  /Applications/Homebrew/Cellar/nodenv/1.3.1: 34 files, 64.9KB, built in 8 seconds
$ nodenv -v
nodenv 1.3.1
```

うむ。入った。そして `node-build` も自動的に入っているみたいだ。


## 初期設定を _.bash_profile_ に書き込む


{% capture text %}

# nodenvの初期化

`nodenv init` を打つと以下のようなメッセージが表示されます。

```sh
$ nodenv init
# Load nodenv automatically by appending
# the following to ~/.bash_profile:

eval "$(nodenv init -)"
```

このメッセージは `~/.bash_profile` に `eval "$(nodenv init -)"` を追加してね、という意味です。
（最初、改行で文章が終わっているのかと思って読んでたらよく意味がわかりませんでした…）

参考：

* [`nodenv init` won't add shims to `$PATH` · Issue #84 · nodenv/nodenv · GitHub](https://github.com/nodenv/nodenv/issues/84#issuecomment-336346292)
* [FAQ · nodenv/nodenv Wiki · GitHub](https://github.com/nodenv/nodenv/wiki/FAQ#node-is-not-available)

vimや任意のエディタ、`echo` コマンドなりで `~/.bash_profile` を修正します。

```sh
$ echo 'eval "$(nodenv init -)"' >> ~/.bash_profile # ~/.bash_profileにeval "$(nodenv init -)"を追加
$ source ~/.bash_profile # リロード
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HomebrewでmacOSにnodenvを導入する（2018年版） - えんぴつぶろぐ](https://empitsu88.hatenablog.com/entry/2018/11/01/000000)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


なるほどなるほど、と _.bash\_profile_ を確認したら、 `rbenv` や `pyenv` にこんな記述があったのを思い出した。

```sh
#rbenv command
export RBENV_ROOT=/Applications/Homebrew/opt/Ruby
eval "$(rbenv init -)"

#pyenv command
export PYENV_ROOT=/Applications/Homebrew/opt/pyenv/Python
eval "$(pyenv init -)"
```

そういえば `***_ROOT` 変数を設定してルートディレクトリを _/Applications/Homebrew_ 内部にまとめていた。
`nodenv` にそういう機能はあるのかな。

{% capture text %}

## Environment variables

You can affect how nodenv operates with the following settings:

| name               | default                                                                       | description                                                                                                      |
|--------------------|-------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `NODENV_VERSION`   |                                                                               | Specifies the Node version to be used.  Also see [`nodenv shell`](https://github.com/nodenv/nodenv#nodenv-shell) |
| `NODENV_ROOT`      | `~/.nodenv`                                                                   | Defines the directory under which Node versions and shims reside. Also see `nodenv root`                         |
| `NODENV_DEBUG`     |                                                                               | Outputs debug information. Also as: `nodenv --debug <subcommand>`                                                |
| `NODENV_HOOK_PATH` | [see wiki](https://github.com/rbenv/rbenv/wiki/Authoring-plugins#rbenv-hooks) | Colon-separated list of paths searched for nodenv hooks.                                                         |
| `NODENV_DIR`       | `$PWD`                                                                        | Directory to start searching for `.node-version` files.                                                          |


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[nodenv/nodenv: Manage multiple NodeJS versions.](https://github.com/nodenv/nodenv#nodenv-shell)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


あった。 _/Applications/Homebrew_ 内部にまとめます。

```sh
#rbenv command
export RBENV_ROOT=/Applications/Homebrew/opt/Ruby
eval "$(rbenv init -)"

#pyenv command
export PYENV_ROOT=/Applications/Homebrew/opt/pyenv/Python
eval "$(pyenv init -)"

#nodenv command
export NODENV_ROOT=/Applications/Homebrew/opt/nodenv/Node
eval "$(nodenv init -)"
```

## Node.js のインストール / ローカルのバージョンを設定

{% capture text %}

# Node.jsのバージョンを選択してインストール

念の為、最新版ではなく推奨版であるNode.jsの10.13.0をグローバルにインストールします。

```sh
$nodenv install 10.13.0
$nodenv global 10.13.0
$nodenv versions # nodenvに10.13.0が追加されたか確認
  system
* 10.13.0 (set by /Users/yoshie/.nodenv/version)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HomebrewでmacOSにnodenvを導入する（2018年版） - えんぴつぶろぐ](https://empitsu88.hatenablog.com/entry/2018/11/01/000000)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


[`rbenv`]({{site.github.url}}{% link _docs/Jekyll/rbenv-homebrew.md %})
や 
[`pyenv`]({{site.github.url}}{% link _docs/Mac/homebrew-pyenv.md %})
と使い勝手はおなじですね。



```sh
$ nodenv install 12.14.0
Downloading node-v12.14.0-darwin-x64.tar.gz...
-> https://nodejs.org/dist/v12.14.0/node-v12.14.0-darwin-x64.tar.gz
Installing node-v12.14.0-darwin-x64...
Installed node-v12.14.0-darwin-x64 to /Applications/Homebrew/opt/nodenv/Node/versions/12.14.0

$ cd Project/Node

$ nodenv versions
  12.14.0

$ nodenv local 12.14.0
$ node -v
v12.14.0
```

Ver.12.14.0 をインストールして、 Node.js を実行するディレクトリに移動して、ローカルのバージョンを設定しています。


## うごかしてみる

{% capture text %}

## Hello world (コンソール版)

まずは、おきまりの Hello world から。`$` は一般ユーザの、`#` は特権ユーザのプロンプトを示します。

sample1.js
: ```js
  console.log("Hello world!");
  ```

Console
: ```sh
  $ node sample1.js
  Hello world!
  ```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Node.js入門](http://www.tohoho-web.com/ex/nodejs.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


やってみる。

```sh
$ vimr sample1.js
lev:Node leico_studio$ node sample1.js 
Hello World!
```

問題なし。


{% capture text %}

## Hello World (Web版)

Web サーバとして動作させる場合のサンプルがこちら。
sample2.js
: ```js
  var http = require('http');
  var server = http.createServer(function(req, res) {
    res.write("Hello world!\n");
    res.end();
  }).listen(8080);
  ```

まず、サーバを起動する。

Console
: ```sh
  $ node sample2.js
  ```

別の端末から、Node.js を呼び出す。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Node.js入門](http://www.tohoho-web.com/ex/nodejs.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

少しモダンに書く練習をしてみる。

```js
const http = require('http');
const server = http.createServer((req, res) => {
  res.write('Hello World!');
  res.end();
}).listen(8080);
```

実行して、web ブラウザからアクセスしたら Hello World! が返ってくるはず。

```sh
$ node sample2.js
```

[http://localhost:8080/](http://localhost:8080) にアクセスしてみる。


{% capture url %}{{site.github.url}}{% link _docs/Mac/images/homebrew-nodenv/01_localhost.png %}{% endcapture %}
{% capture caption %}

成功。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


サーバはターミナルで `Ctrl - C` で終了できた。
