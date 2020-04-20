---
layout : post
title  : 総和について
date   : 2020/04/20
lastchange : 2020-04-21 00:28:45.
tags   :
  - math
  - sum
  - sigma
  - シグマ
---

## 総和

\\[
\sum
\\]

この記号で表される数式のこと。決められた範囲の数を全て足し合わせることを示す。

\\[
\sum_{i = 1}^{n} x_i = x_1 + x_2 + x_3 + \cdots + x_n
\\]

ということを表す。

\\[
\sum_{N} x_{N}
\\]

と、範囲の部分に数の集合を意味するものが入ったりすることもある。


### 読み方・由来

{% capture text %}
\\( \sum \\) 記号の考案者は、オイラー(Leonard Euler，1707-1783）です。「無限解析序論」の中です。

\\[
\exp(x) ＝ 1 + x + \frac{1}{2!} x ^ 2 + \frac{1}{3!} x ^ 3 + \cdots\cdots + \frac{1}{n!} x ^ n + \cdots 
\\]

を、

\\[
\exp(x) = \sum_{k = 0}^{ \infty } \frac{1}{k!} x ^ k
\\]

と書きます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[\\( \sum \\) の由来を教えて下さい。 数学 \| 教えて!goo](https://oshiete.goo.ne.jp/qa/570786.html)

by [kbannai](https://oshiete.goo.ne.jp/profile/46822/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}



読み
: sum
: 総和
: シグマ

由来
: {% include cite.html text=text source=source %}

例
: \\(\displaystyle \sum_{i=0}^{n} x_i \\)
    : \\( i = 0 \\) から \\( n \\) までの \\( x_i \\) の合計/合算/総和
    : sum of zero to n

### 総和に乗算する

\\[
\sum_{i=1}^n a_i = a_1 + a_2 + a_3 + \cdots + a_n
\\]

総和に \\(C\\) を乗算する

\\[
C\sum_{i=1}^n a_i = C( a_1 + a_2 + a_3 + \cdots + a_n) \tag{1}\label{sigma_1}
\\]

\\( \eqref{sigma_1} \\) を展開する

\\[
Ca_1 + Ca_2 + Ca_3 + \cdots + Ca_n \tag{2}\label{sigma_2}
\\]

\\( \eqref{sigma_2} \\) から


\\[
\begin{eqnarray}
  C \sum_{i=1}^n a_i &=& Ca_1 + Ca_2 + Ca_3 + \cdots + Ca_n \\\\\
                     &=& \sum_{i=1}^n Ca_i
\end{eqnarray}
\\]


