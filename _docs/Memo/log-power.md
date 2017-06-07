---
layout : post
title  : 対数、指数っぽい関数を考える
date   : 2017/04/22
lastchange : 2017-06-07 21:49:51.
tags   :
  - power
  - math
  - log
  - logarithmic
---

## 思いついた

\\(0 \to 1\\)の間で指数／対数っぽいカーブをする関数一般的に利用しやすいやつなにかないかと考えていた時、

べき乗使えばいいんでね！？ ってなった。

なぜかというと \\(y = x^n \\) の時

1. \\( x = 0 \\) の時必ず \\( y = 0 \\)
2. \\( x = 1 \\) の時必ず \\( y = 1 \\)
3. \\( n = 1 \\) の時、\\( y \\) は \\( x \\) に比例
4. \\( n \gt 1 \\) の時は指数っぽい曲線になる
5. \\( 0 \lt n \leqq 1 \\) の時は対数っぽい曲線になる
6. 対数曲線 \\( log_{a}x \\)、指数曲線 \\( a^x \\) と関数を分けることなく使える

ちなみに \\( 0 \lt n \leqq 1 \\) のべき乗というのは平方根などになる。
\\[
x^\frac{1}{n} = \sqrt[n]{x}
\\]

{% capture url %}{{site.github.url}}{% link _docs/Memo/images/log-power/01_power.png %}{% endcapture %}
{% assign caption = '
いろんなべき乗のグラフ
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## \\( log_{a}x \\)との比較

{% capture url %}{{site.github.url}}{% link _docs/Memo/images/log-power/02_compare.png %}{% endcapture %}
{% assign caption = '
\\(log_{a}x\\) グラフと \\( x^{\frac{1}{n}} \\) グラフの比較
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

 安直に \\( x^{\frac{1}{n}} \\) とすると\\( log_{a}x \\) に比べ急峻なカーブになる。
 この辺りは感覚で調整してゆくしかないか。
