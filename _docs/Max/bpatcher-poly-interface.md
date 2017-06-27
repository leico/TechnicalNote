---
layout : post
title  : "poly~のUIをbpatcherで作る"
date   : 2017/06/27
lastchange : 2017-06-27 17:42:32.
tags   :
  - Max
  - Max for Live
  - bpatcher
  - namespace
  - poly
---

## `poly~`のUIを`bpatcher`で作ると捗る

[bpatcher小技まとめ]({{site.github.url}}{% link _docs/Max/bpatcher-tips.md %})、
[poly~:実体毎に個別のメッセージ/シグナルを送信する]({{site.github.url}}{% link _docs/Max/poly-individual-signal.md %})
を応用して、`poly~`で実体ごとに個別の処理を制御するUIを`bpatcher`で作成するとめんどくさいけど楽に作成することができる。

[RAY.MTR](https://cycling74.com/projects/ray-mtr)もこの方法を用いて作成しているので、RAY.MTRの中身を見ながら解説する。


## 引数によって`poly~`の実体を指定する

これに限る。`poly~`の実体番号を利用することで個別のメッセージ/シグナルを
`send`/`receive`で送受信することができるので、実体番号を`bpatcher`の引数に与えることで、
個々の実体を効率よく制御できる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/01_8ch.png %}{% endcapture %}
{% assign caption = '
この部分で8chのサンプラー&各種エフェクトを実装している。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/02_8ch_all.png %}{% endcapture %}
{% assign caption = '
展開するとこんな感じで
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/03_args.png %}{% endcapture %}
{% assign caption = '
個々は`bpatcher`をコピーしただけ。ただし引数が全て異なる
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/04_sampler.png %}{% endcapture %}
{% assign caption = '
1つの`bpatcher`内部にはこれだけの処理が入っている。
これを全て1つのパッチファイルにコピペ繰り返して、`send`/`receive`の置き換えをやっていくのは非常に手間がかかる。

`bpatcher`の引数による名前空間を使うと1箇所変更するだけで、この場合約50の`send`/`receive`が更新される。
とっても楽ちん
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/05_bpatcher_entrance.png %}{% endcapture %}
{% assign caption = '
`bpatcher`側は送受信先を引数で指定し、
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/bpatcher-poly-interface/06_poly_entrance.png %}{% endcapture %}
{% assign caption = '
`poly~`側は実体番号で送受信先が決定される。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


RAY.MTR自体も`bpatcher`から呼び出し、引数で名前空間を作っている。
RAY.MTRに与えている名前空間は `#0_RAY_MTR` 。内部の名前空間はこれを継承する設計になっているので、
内外を結ぶ`send`/`receive`の名前は全て `#0_RAY_MTR_xxx` となっている。
他のパッチと衝突することはない。
