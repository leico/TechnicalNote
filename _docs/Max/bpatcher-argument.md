---
layout : post
title  : "bpatcher: 引数を利用する"
date   : 2017/06/01
lastchange : 2017-06-07 21:51:30.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - args
  - argument
---

## 引数をサブパッチに与える

`patcher`で引数を利用するには`patcherargs`の出力を利用する必要があったが、
`bpatcher`では`#1 #2 #3...`という埋め込み型の引数を利用することができる。

別ファイルのパッチをサブパッチとして読み込む場合に利用可能だったものが、
内包するパッチでも利用できる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-argument/01-args.png %}{% endcapture %}
{% assign caption = 'インスペクタの **Argument(s)** を編集すると' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-argument/02-substitute.png %}{% endcapture %}
{% assign caption = 'スペース区切りで左から`#1 #2 #3...` にそれぞれの値が代入される' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

