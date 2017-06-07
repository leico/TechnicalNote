---
layout : post
title  : "bpatcher: 名前に引数を利用する"
date   : 2017/06/01
lastchange : 2017-06-07 21:51:08.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - args
  - argument
  - name
  - send
  - receive
---

## 引数を`send`/`receive`の名前にする

`send`/`receive`の名前も引数で与えることができる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-argument-name/01-send.png %}{% endcapture %}
{% assign caption = '引数に **value1** を与えているので `s value1`になっている' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

内部で用いる`send/receive`の名前を一意にしたい場合は`#0`を用いればよいが、
同じ処理をするパッチが複数必要で、それぞれ`bpatcher`内外で`send`/`receive`を用いたい時にこの方法は有効。

`#0`を用いてしまうと毎回名前が変化してしまうため、内外での`send`/`receive`は困難になる。


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-argument-name/02-parallel.png %}{% endcapture %}
{% assign caption = '
この程度なら`bpatcher`に`outlet`を作るだけでもよいが、
それでは難しい場合もある。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

