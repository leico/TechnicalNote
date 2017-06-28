---
layout : post
title  : "pattrの名前は先頭に「---」付けてはならない"
date   : 2017/06/28
lastchange : 2017-06-28 18:07:46.
tags   :
  - Max for Live
  - pattr
  - parameter
  - save
  - load
---

## `pattr`にパラメータが保存されない問題

[Max for Live: 前回利用したパラメータが保存され、読み込まれるようにする]({{site.github.url}}{% link _docs/Live/store-parameter.md %})
で`pattr`の *Parameter* にチェックを入れると、
次回読み込んだ際に前回の値がロードされるというのが判明したが、
読み込まれない問題が発生した。

## `---`ついていると保存されない

{% capture text %}
# Using Symbols in Max for Live

The "name space" in Max is global - when you have objects 
that have names associated with them such as 
[coll](https://docs.cycling74.com/max7/maxobject/coll), 
[send](https://docs.cycling74.com/max7/maxobject/send),
[receive](https://docs.cycling74.com/max7/maxobject/receive),
[table](https://docs.cycling74.com/max7/maxobject/table),
or [buffer~](https://docs.cycling74.com/max7/maxobject/buffer~),
you can share data between Max for Live devices.
In these cases, the Max name space is shared, but the "signal processing space" is independent - each Max for Live device processes its audio or data separately.

If you want a named object to be unique to a device, 
use three dashes `---` to start the name of your 
[buffer~](https://docs.cycling74.com/max7/maxobject/buffer~)
or [send](https://docs.cycling74.com/max7/maxobject/send)
/ [receive](https://docs.cycling74.com/max7/maxobject/receive)
 destination (e.g. `s ---filtercutoff`).
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Using Symbols in Max for Live](https://docs.cycling74.com/max7/vignettes/live_symbols)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

そもそも`---`って何かというと、Max for Live デバイスに固有のIDを振り、
他のMax for Liveデバイスと名前が被らないようにするための仕組み。
通常のMaxで利用する`#0`のような役割。

これを`pattr`の名前で利用してしまうと、前回のパラメータが保存できない。
正確に言えば、前回のパラメータにアクセスすることができなくて初期化されてしまう。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-name/01_patch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    549.3oc2V00apCCC841eEU4Y5TSgxG2+JnITJDXYpMAkl1KSS6+9bbZ5JasC
    F2Id39.AE6Deb7w9.uFFPxUm3Ujn+DsNJH30vf.zj0PP69.RI6z1BVEdLx9B
    krtjLw4pgokrRN5ALmy0qoO5cBFDxBtAuHs03QMuhKMLiPI2n4aMNvoIKdHY
    RzzU10rD6ZJ8gjnGau1dktjgmcdqkJyKENf6gmp13AL0CHyr8Ig7POvxnoV.
    nKVNBZ8Sx9IuK7lWNxcABfNhjyjGHcWUrCyIU9ywzDRWNngpjgq2vkrbWZmX
    c8VXncYxUV7k7+BAdnhebbrgWY7tL7S3a095M5nO48FHFjWVt5KUpKwBSGmE
    ls.iLMc4Hw9GwB3mAYgTelAgynzsWJo+if0v2sANLjaarELQdswMVDzwDADl
    TpZqOc081XOLESQe.81Glt3+MH0vJp4p8dyd68QAFCOLB6e14pdRoMWyA80y
    DmKryz+0OrM8RZD2TeX18SeX5pr6h9.89oO7MhC2rxPVmvvB5uovvz4q5ILL
    Pr+cDFVNnt.fy7+eDFFaXuDZavgkYPwcH2BrvFOh+yEU9GTTvJMoPH+7eBAa
    Kr1OuauRUq25Y2VRLh1wW6fLQH65KV+wbVDz8L3.00Bj8mQtLRIeAI2Kjc7X
    CWW0dZDDXx8Yk1tcwDbqP51hSFPSYive9YnElFFTLPmXsqWkbZ9Lh6ppcbsr
    Vz1ZAH+V36PbS8U+
    -----------end_max5_patcher-----------

右側、`pattr ---test`は固有のIDが振られ`pattr 016test`になっている。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-name/02_inited.png %}{% endcapture %}
{% assign caption = '
再読み込みを行うと`pattr 017test`になり、保存していたデータにアクセスできなくなっているようだ。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`---`で名前被りを防ぐ方法が使えないとなると、複数のデバイスを用いることができない？　検証する。

## `---`なくても他のデバイスと干渉しない

ネタバレ甚だしい小見出しだが、下の画像の状態で検証した。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-name/03_multipattr.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    462.3ockTssaiCBD8Y7WAhmSiL4d2ekppHbxjTprgHL1MUU8eeGF.W2sNsae
    HDwLCyY3bN32JXhJ6UnUv+C+ANi8VAiQgBAXo8LQi55gZUKUl3Ts0z0HlES0
    qbFUCPYvvUfKmA2oM0fmNkLE7hCZAiW40VydGbvGQVtdd4L9x6CqqKCqKjyK
    4OlN0IqqQQktIEo0+ZcDzQvY67Y7VjwS4O7j1bdDVKum.Stc2MPa7LNd1is2
    +5EH1HDZtnRYNKFNp9HMS1pmuSJECyfCYHO31CFUUbrKCodunHrL6+j3MvKX
    imh38PqOG2CWoKZ3p6c7wo98JBIHakegh9I5e42P+anlJWr6F89WQ+zuon+c
    4AC6l25Rmob95kebIT8vw8Xw3rsOPV5pNe7s.aPBXBkwXSzy.gmZ9zZqjxg5
    5XXF5+2fTuptCrmxgywGiB9167Tx9mJpAsMzikUH4NUZMQr2ci7sOYc9eDjr
    PTFSQd47eIiMwzhZs4e+.CYKBw+rau014NjU2jHxkC50QbRzlAewCe7Niitm
    ufq5xkdv0lplv.eO8r0E1tcFsUahaI+JZU5045WQQTNz95Q+QWzAIttYkHdT
    6QvY5zIAGQ98h+xZWhD9
    -----------end_max5_patcher-----------

3つの同じデバイスを展開。それぞれ異なる数値を再読み込みさせる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-name/04_loaded.png %}{% endcapture %}
{% assign caption = '
無事に全て読み込まれた。`pattr`の名前に`---`をつける必要がない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`pattr`は今のところ、名前に`---`をつける必要がない。
逆につけてしまうと前回のデータを読み込むことができない。
だから名前空間のところで`pattr`に言及されていないのか。
