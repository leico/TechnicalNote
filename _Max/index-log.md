---
layout : post
title  : "Max: 指数/対数カーブ特性をつける"
date   : 2017/03/21
lastchange : 2017-03-30 01:38:17.
tags   :
  - Max
  - log
  - indices
  - index
  - logarithm
  - Max for Live
---

## 指数/対数特性の必要性

音を扱う場合に *dB(デシベル)* というものからはなかなか逃れられないもの。
*dB* という単位は音を利用する際 *音圧(dB SPL)* や *音の強さ(dBm)* 、 *レベル(dBu)* などを調整する際に遭遇する。

*dB* が表すのは感覚量であり、対数特性をもっている。音の強さが *x* から *2x* と倍になったところで、
人間の感覚的には *1.3x* 程度にしか変化していないように感じる。ということを数値化したものである。

同様に、音程を表す *octave* も感覚量であり、対数特性を持つ。音に関わると対数ばかりだ。

そして、対数特性を打ち消すには指数特性的にパラメータを操作しなければならないということになる。
ので、指数/対数特性でパラメータを操作できると捗る。

{% capture url %}{{ site.github.url }}{% link _Max/images/index-log/01_index-log.png %}{% endcapture %}
{% assign caption = '
*対数(緑線)* とその特性を打ち消す *指数(赤線)* 、及び打ち消して線形化したもの(青線)
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

