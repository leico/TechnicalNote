---
layout : post
title  : 指数、対数について
date   : 2020/04/20
lastchange : 2020-04-20 13:53:39.
tags   :
  - math
  - log
  - exponential
---

## 指数関数

\\[
y = x^a
\\]

の形式で表される関数

### 読み方・由来

読み
: exponent
    : 指数関数
: 累乗
    : るい乗
: 冪乗
    : べき乗

由来
: \\( x \\) を \\( n \\) 回掛け合わせたものを \\( x^n \\) と表記する
: \\[
x^n = \overbrace{x \times x \times x \times \cdots \times x}^{n}
\\]


例
: \\( x^n \\)
    : \\( x \\) の \\( n \\) 乗


指数
: \\( x^n \\) の \\( n \\) のことを指数という

## 対数関数

\\[
y = log_{a}x
\\]

の形式で表される関数

### 読み方・由来

{% capture text %}
底を b とする x の対数（英: logarithm of x to base b; base b logarithm of x）
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[対数 - Wikipedia](https://ja.wikipedia.org/wiki/%E5%AF%BE%E6%95%B0)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}


読み
: 対数
: ログ

由来
: logarithm
    : 対数

例
: \\(\displaystyle \log_{b} (x) \\)
    : ログ エックス
    : {% include cite.html text=text source=source %}




## 指数関数と対数関数の変換

\\[
a^p = M \Longleftrightarrow p = \log{a}M
\\]

指数関数 \\(a^p = M\\) の指数 \\(p\\) は \\(a\\) を底とする対数関数 \\(\log{a}M\\) の解として求めることができる。


