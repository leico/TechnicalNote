---
layout : post
title  : "bpatcher小技まとめ"
date   : 2017/06/26
lastchange : 2017-06-27 18:17:25.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - embed
  - args
  - argument
  - namespace
  - inherit
---

## UI作成を`bpatcher`を使って賢く便利に

`bpatcher`のめんどくさくて賢い使い方をまとめました。



## `patcher`のようにパッチ内部に`bpatcher`を埋め込む

`bpatcher`はサブパッチの内容を表示しつつ、
そのパッチの機能を利用することができるオブジェクトだが、
普通の`patcher`のように親となるMaxパッチにサブパッチを埋め込むことができる。

方法は、インスペクタの **Embed Patcher in Parent** にチェックを入れるだけ

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/01-embed.png %}{% endcapture %}
{% assign caption = '
`bpatcher`作成時に

    bpatcher name @embed 1

でも可能

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

埋め込まれたサブパッチを編集するには

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/02-view.png %}{% endcapture %}
{% assign caption = '右クリックメニューから **Object -> New view of Embedded Patcher** を選択し' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/03-unlock.png %}{% endcapture %}
{% assign caption = '表示されたサブパッチを編集すると反映される' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## 引数をサブパッチに与える

`patcher`で引数を利用するには`patcherargs`の出力を利用する必要があったが、
`bpatcher`では`#1 #2 #3...`という埋め込み型の引数を利用することができる。

別ファイルのパッチをサブパッチとして読み込む場合に利用可能だったものが、
内包するパッチでも利用できる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/04-args.png %}{% endcapture %}
{% assign caption = 'インスペクタの **Argument(s)** を編集すると' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/05-substitute.png %}{% endcapture %}
{% assign caption = 'スペース区切りで左から`#1 #2 #3...` にそれぞれの値が代入される' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## 引数を`send`/`receive`の名前にする

`send`/`receive`の名前も引数で与えることができる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/06-send.png %}{% endcapture %}
{% assign caption = '引数に **value1** を与えているので `s value1`になっている' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

内部で用いる`send/receive`の名前を一意にしたい場合は`#0`を用いればよいが、
同じ処理をするパッチが複数必要で、それぞれ`bpatcher`内外で`send`/`receive`を用いたい時にこの方法は有効。

`#0`を用いてしまうと毎回名前が変化してしまうため、内外での`send`/`receive`は困難になる。


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/07-parallel.png %}{% endcapture %}
{% assign caption = '
この程度なら`bpatcher`に`outlet`を作るだけでもよいが、
それでは難しい場合もある。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





## サブパッチに自分の引数をアレンジして利用する

`bpatcher`の内部に`bpatcher`を作るといった事はもちろん可能で、
それぞれ引数を利用することも可能。

そしてサブパッチの引数に自身の引数を利用することができる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/08-inherit.png %}{% endcapture %}
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




## プレゼンテーションモードでサブパッチを展開する

*open in presentation* にチェックが入っているサブパッチはプレゼンテーションモードで開かれる。
内部埋め込みされているサブパッチも **Command** + **Shift** + *i* でパッチャーインスペクタを開き、
*opent in presentation* にチェックを入れた後、再度パッチを開き直すか、`bpatcher`をコピーすると
プレゼンテーションモードでサブパッチが表示される。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-tips/09-presentation.png %}{% endcapture %}
{% assign caption = '
埋め込んだサブパッチをプレゼンテーションモードで表示している例。
もちろん`bpatcher`自体をプレゼンテーションモードに登録すれば、メインパッチのプレゼンテーションモードにも表示される。

UI部分を含めてサブパッチを作り、プレゼンテーションでUIだけ表示していろんなパッチで使いまわすことも可能。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

