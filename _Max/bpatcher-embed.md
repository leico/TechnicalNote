---
layout : post
title  : "bpatcher: 外部パッチを読み込むのではなく内包する"
date   : 2017/06/01
lastchange : 2017-06-01 22:05:52.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - embed
---

## `patcher`のようにパッチ内部に`bpatcher`を埋め込む

`bpatcher`はサブパッチの内容を表示しつつ、
そのパッチの機能を利用することができるオブジェクトだが、
普通の`patcher`のように親となるMaxパッチにサブパッチを埋め込むことができる。

方法は、インスペクタの **Embed Patcher in Parent** にチェックを入れるだけ

{% capture url %}{{ site.github.url }}{% link _Max/images/bpatcher-embed/01-embed.png %}{% endcapture %}
{% assign caption = '
`bpatcher`作成時に

    bpatcher name @embed 1

でも可能

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

埋め込まれたサブパッチを編集するには

{% capture url %}{{ site.github.url }}{% link _Max/images/bpatcher-embed/02-view.png %}{% endcapture %}
{% assign caption = '右クリックメニューから **Object -> New view of Embedded Patcher** を選択し' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _Max/images/bpatcher-embed/03-unlock.png %}{% endcapture %}
{% assign caption = '表示されたサブパッチを編集すると反映される' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


