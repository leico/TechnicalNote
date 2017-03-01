---
layout : post
title  : rbenvコマンドの利用方法
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## `rbenv install`について

`rbenv`のインストールだけでは`rbenv install`は実行できず、Rubyのインストールができない。
`rbenv`のインストール時に同時に`ruby-build`をインストールしているのはそのためである。

### `rbenv install -l`でインストールする／できるバージョンの確認

`rbenv install -l`を実行するとかなりの量のバージョン一覧が出力される。
利用したいRubyのバージョンをここで確認する。

例えば、{{page.date | date: "%Y-%m-%d"}}現在
[Github Pages - Dependency versions](https://pages.github.com/versions/)
に対応するものの場合、2.3.3となる。

### `rbenv install x.y.z`でRuby Ver.x.y.zをインストール

`rbenv install x.y.z`で`${RBENV_ROOT}/versions/x.y.z`に_Ruby x.y.z_がインストールされる。
場合によっては同時にOpenSSLや、必要となる他のライブラリも一緒にインストールされる。

インストール時間はちょっと長め。数分ほど待つ。

## `rbenv rehash`でRuby関連コマンドの更新

インストールした_Ruby x.y.z_はまだ言うなればパスが通ってない。
`rbenv rehash`コマンドで_Ruby x.y.z_を利用できる状態にする。

この処理はいずれかのバージョンのRubyにgemをインストールした場合も必要になる。

参考:[rbenv rehashは何をやっているのか？](http://dqn.sakusakutto.jp/2014/02/rbenv_rehash_what_it_does.html)

## `rbenv versions`でインストールされているRubyのバージョン確認

インストールされているRubyのバージョン一覧は`rbenv versions`で一覧が出力される。
macOSに標準で入っているバージョンも`system`と表示される。


## `rbenv global [version]`基本的に用いるRubyのバージョンを設定

デフォルトでどのバージョンのRubyを用いるかを`rbenv global [version]`で設定する。

### 2.3.3で設定する場合

```sh
rbenv global 2.3.3
```

### `system`に戻す場合

```sh
rbenv global system
```

## `rbenv local [version]`カレントディレクトリ内で利用するRubyのバージョンを設定

カレントディレクトリで利用するRubyのバージョンを指定する場合`rbenv local [version]`で設定する。

### `~/project`ディレクトリで2.0.0を利用する場合

```sh
cd ~/project
rbenv local 2.0.0
```

### 元に戻す場合

```sh
cd ~/project
rbenv local --unset
```

### `.ruby-version`ファイル

`rbenv local`を実行すると、カレントディレクトリに`.ruby-version`ファイルが生成される。
中身はRubyのバージョンが書かれただけの1行のテキストファイル。

#### 2.0.0を設定した場合の`.ruby-version`ファイル

```sh
$ cat .ruby-version
2.0.0
```

`.ruby-version`ファイルは`rbenv local --unset`すると消失する。

## rbenvでインストールしたRubyで実行する

`rbenv local x.y.z`や`rbenv global x.y.z`が設定されている場合、
`rbenv`でインストールされたRubyが利用されます。

## rbenvでインストールしたRubyにgemを追加する

### `rbenv local x.y.z`/`rbenv global x.y.z`が設定されている場合

`gem install`で`rbenv`のRubyにgemがインストールされます。

### `system`が設定されている場合

`gem install`をするとデフォルトで入っているRubyで実行され、`system`のRubyにインストールされてしまいます。

### Bundlerをインストールする場合

```
gem install bundler
```


