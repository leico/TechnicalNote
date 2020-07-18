---
layout : post
title  : "リポジトリルートから submodule ディレクトリで git コマンドを実行する"
date   : 2020/07/17
lastchange : 2020-07-18 13:59:24 
tags   :
  - git
  - C option
  - submodule
---

## 見たことないオプション

どこで見かけたか思い出せないが、 `-C` オプションが出てきてなんだろうこれ、となったので書いておく

参考:

* [cdせずに任意のgitレポジトリを操作する-Cオプションの紹介 · DQNEO起業日記](http://dqn.sakusakutto.jp/2014/10/cd_git_c.html "cdせずに任意のgitレポジトリを操作する-Cオプションの紹介 · DQNEO起業日記")
 

{% capture text %}
たとえばrbenvをアップデートしたいとき、

```sh
$ cd ~/.rbenv
$ git pull
$ cd ~/.rbenv/plugins/ruby-build
$ git pull
```

なんてしますよね。
でもいちいち cd するの面倒くさいと思いませんか？

実はgitには -C オプションというのがあって、これを使うと cd せずにレポジトリの場所を指定することができます。

```sh
git -C <レポジトリの場所> サブコマンド
```

という風に使います。

なので、さっきの rbenvを更新する例はこのように書けます。

```sh
$ git -C ~/.rbenv  pull
$ git -C ~/.rbenv/plugins/ruby-build  pull
```

シェルスクリプとかcrontabから git を実行するときは大変便利です。 
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[cdせずに任意のgitレポジトリを操作する-Cオプションの紹介 · DQNEO起業日記](http://dqn.sakusakutto.jp/2014/10/cd_git_c.html "cdせずに任意のgitレポジトリを操作する-Cオプションの紹介 · DQNEO起業日記")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

むしろ、 submodule を操作するのがとても便利だ。