---
layout : post
title  : "git submodule でリポジトリの一部だけを利用する(sparse checkout)"
date   : 2020/07/18
lastchange : 2020-07-18 23:25:54
tags   :
  - git
  - sparse checkout
  - submodule
---

## submodule の中身を必要な部分だけ持ってくるようにする

[sparse checkout でリポジトリの一部を持ってくる]({{site.github.url}}{% link _docs/Git/sparse-checkout.md %})
でリポジトリの一部分だけ取得することができた。それを **submodule** に応用してみる。

参考:

* [Git 特定のフォルダのみcloneする submodule編 - Qiita](https://qiita.com/icoxfog417/items/89e55947e9e8f2c16077 "Git 特定のフォルダのみcloneする submodule編 - Qiita")


## submodule を sparse checkout する

{% capture text %}

[前回](http://qiita.com/icoxfog417/items/8b16681213d0fd33602c)
Gitで特定フォルダのみクローンする方法を書いたが、これはsubmoduleの場合も使用可能。
というか、submoduleの時にこそ使用する機会が多いだろう(dllだけ取るなど)。

ただ、ファイルを置く場所がちょっと異なる。submodule関連の設定は `.git/modules` 配下にあるため、
sparse-checkoutもこちらに置く必要があるのだ。

1. `git submodule add https://some/module.git somemodule`
2. `git commit -m "add module"`
3. `cd somemodule`
4. `git config core.sparsecheckout true`
5. `echo I_want/this_folder/ > ../.git/modules/somemodule/info/sparse-checkout`
6. `echo need/this_folder_too/ >> ../.git/modules/somemodule/info/sparse-checkout`
7. `git read-tree -mu HEAD`

2のcommitは、この段階でまだコミットが一回もないなら必要。最後のread-treeでHEADを読むため、一回はcommitしておかないとうまく動かない。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Git 特定のフォルダのみcloneする submodule編 - Qiita](https://qiita.com/icoxfog417/items/89e55947e9e8f2c16077 "Git 特定のフォルダのみcloneする submodule編 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ちょっと記事が古いので心配だが、やってみる。
テストリポジトリを作り、bootstrap 辺りを submodule で追加して試してみる。

まずはリポジトリを用意する。

```sh
$ git clone https://github.com/leico/sparse_checkout_test.git
Cloning into 'sparse_checkout_test'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
```

中身はこんな感じ

```sh
$ cd sparse_checkout_test 
$ ls
README.md
```

bootstrap を submodule で追加する

```sh
$ git submodule add https://github.com/twbs/bootstrap.git bootstrap
Cloning into '/Users/leico_studio/Project/public_git/sparse_checkout_test/bootstrap'...
remote: Enumerating objects: 154371, done.
remote: Total 154371 (delta 0), reused 0 (delta 0), pack-reused 154371
Receiving objects: 100% (154371/154371), 156.67 MiB | 6.98 MiB/s, done.
Resolving deltas: 100% (101529/101529), done.
```

中身を確認する

```sh
$ ls bootstrap 
CODE_OF_CONDUCT.md	composer.json		package-lock.json
LICENSE			config.yml		package.js
README.md		dist			package.json
SECURITY.md		js			scss
build			nuget			site
```

bootstrap 内で `git config core.sparsecheckout true` をする。
bootstrap フォルダに移動せずに実行できる `-C` オプションを使った。

```sh
$ git -C bootstrap config core.sparsecheckout true
```

設定を確認する

```sh
$ git -C bootstrap config -l
credential.helper=osxkeychain
filter.lfs.required=true
filter.lfs.clean=git-lfs clean %f
filter.lfs.smudge=git-lfs smudge %f
user.name=*****
user.email=*****
core.excludesfile=~/.gitignore
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
core.worktree=../../../bootstrap
core.sparsecheckout=true
remote.origin.url=https://github.com/twbs/bootstrap.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.main.remote=origin
branch.main.merge=refs/heads/main
```

_.git/modules/bootstrap/info/sparse-checkout_ の中身を書き換える
とりあえずリポジトリ直下の _js/_ だけにしてみる。

```sh
$ vimr .git/modules/bootstrap/info/sparse-checkout 
```

```
/js/
```

`git read-tree -mu HEAD` で bootstrap のツリー情報を読み込み直す

```sh
$ git -C bootstrap read-tree -mu HEAD
```

_bootstrap_ ディレクトリ以下を確認してみる

```sh
$ ls bootstrap 
js
```

_js/_ だけになった。



## 参照する項目を増やす

参照する項目を増やしてみる。
例として _.git/modules/bootstrap/info/sparse-checkout_ に _dist_ を追加してみる。

```sh
$ vimr .git/modules/bootstrap/info/sparse-checkout 
```

```
/js/
/dist/
```

そしたら `read-tree` をもう一度 _bootstrap_ ディレクトリで実行する。

```sh
$ git -C bootstrap read-tree -mu HEAD
$ ls bootstrap 
dist	js
```

_dist_ ディレクトリも参照できた。

## 別のバージョンに checkout してみる

bootstrap を選んだのはこのためが大きい。別のバージョンに checkout したら
果たして sparse checkout した状態でそのバージョンを参照することになるのだろうか。
やってみる。

まずは現状のバージョン確認

```sh
$ head -n 3 bootstrap/dist/css/bootstrap.min.css
/*!
 * Bootstrap v5.0.0-alpha1 (https://getbootstrap.com/)
 * Copyright 2011-2020 The Bootstrap Authors
```

先頭を表示したら `v5.0.0-alpha1` ということがわかった。

bootstrap のリリース一覧を確認し、 `v3.4.1` に変更してみる。

```sh
$ git -C bootstrap checkout v3.4.1
Previous HEAD position was cc76849d0 Bump @rollup/plugin-commonjs from 13.0.1 to 14.0.0 (#31291)
HEAD is now at 68b0d231a Dist
```

変わったらしい。確認する。

```sh
$ head -n 3 bootstrap/dist/css/bootstrap.min.css
/*!
 * Bootstrap v3.4.1 (https://getbootstrap.com/)
 * Copyright 2011-2019 Twitter, Inc.
```

参照しているファイルも確認する。

```sh
$ ls bootstrap 
dist	js
```

ちゃんと変更することができた。

## sparse checkout の問題点

この方法は問題点があって、

{% capture text %}

これで、必要な部分だけ使いリポジトリを構成できて一安心。。。
と思いきや、この設定( `.git` 内の設定)はcommitして記録することができないので、
チームで共有するということができない。つまり各人が上記のコマンドを打っていく必要があり、
サブモジュールが多い場合めげるだろう。

この点についてはうまい方法がないか調査中。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Git 特定のフォルダのみcloneする submodule編 - Qiita](https://qiita.com/icoxfog417/items/89e55947e9e8f2c16077 "Git 特定のフォルダのみcloneする submodule編 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

つまり、新たに `git clone` した場合はもう一度同じことをしなければならない。

容量に関して問題となるのは手元に全てのデータが存在する時で、
サーバ側にはリンク先のコミットハッシュのみが保存されるということを考えれば、
確かにサーバ側に sparse checkout を持っている必要はない。
むしろ、sparse chekout の状態をサーバが保持することで、
参照先のデータが断片化してしまうほうが問題である。
納得。

ではどうするかというと、 shell script を作るしか無いような気がする。