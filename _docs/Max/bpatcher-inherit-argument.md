---
layout : post
title  : "bpatcher: 子の名前に自身の引数を利用する"
date   : 2017/06/01
lastchange : 2017-06-07 21:52:11.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - args
  - argument
  - name
  - send
  - receive
  - inherit
---

## サブパッチに自分の引数をアレンジして利用する

`bpatcher`の内部に`bpatcher`を作るといった事はもちろん可能で、
それぞれ引数を利用することも可能。

そしてサブパッチの引数に自身の引数を利用することができる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-inherit-argument/01-inherit.png %}{% endcapture %}
{% assign caption = '

パッチ構成

* パッチ
    * `bpatcher parent`
        * `r #1_child_number`(r parent_child_number)
        * `bpatcher #1_child`(bpatcher parent_child)
            * `flonum`
            * `s #1_number`(parent_child_number)

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}
