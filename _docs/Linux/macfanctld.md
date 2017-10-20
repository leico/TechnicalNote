---
layout : post
title  : macintoshにいれたLinuxでの空冷ファンコントロール
date : 2017/10/18
lastchange : 2017-10-20 13:29:10.
tags   :
  - Linux
  - Macintosh
  - Macbook
  - macfanctld
---

## デフォルトではファンが動作しない

ので、Macbook Proのファンを自動調整してくれるプログラムを探した。

参考 : 

* [Mac内部のFanをコントロール - Linux Mintのメモ](https://sites.google.com/site/linuxnomemo/mint-setting/macfancontrol)


## macfanctldを使う


{% capture text %}

## Mac用ファンコントロールデーモン

他になにかいいパッケージ無いかなと調べていたらパッケージリストの中に`macfanctld`がありました。
このパッケージはMac Book、Mac Book Pro用のファンコントロールデーモンです。
Mac miniはファンが1個しか無いのでこれを使ってファンの回転数を調整してあげれば大丈夫そうです。

パッケージマネージャから`macfanctld`をインストールすると、すぐにデーモンとして起動します。
動作開始直後はAppleSMCの設定よりも`macfanctld`の初期設定のほうが
温度センサーによる設定が厳しいのでファンが高速回転しますが、
しばらくすると温度が下がるため回転数は下がっていきます。

---

## Mac miniなら大丈夫そう

実際にMac miniで動作させてみた時のファンの回転数を見てもMac miniでも適正値なようですので、
ファンの音が気にならなければ初期値のまま運用しても問題無さそうです。
ただ、ファンの回転数が最初より上がるためファンの音が少し大きくなります。
気になる場合は最低値を1500RPMに変更しておいてもいいかもしれません。

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Mac内部のFanをコントロール - Linux Mintのメモ](https://sites.google.com/site/linuxnomemo/mint-setting/macfancontrol)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ということなので`macfanctld`を探してインストールする

```sh
$ apt-cache search macfanctld
macfanctld - fan control daemon for Apple MacBook / MacBook Pro computers
$ sudo apt-get install macfanctld
```


完了。インストールした瞬間からファンが動き出す。
