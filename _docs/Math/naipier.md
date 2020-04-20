---
layout : post
title  : 自然対数の底について
date   : 2020/04/20
lastchange : 2020-04-20 14:02:36.
tags   :
  - math
  - log
  - exponential
  - napier
  - 自然対数の底
  - ネイピア数
---

## 自然対数の底(ネイピア数)

{% capture text %}

## ネイピア数とは

ネイピア数 \\(e=2.71828182845904 \cdots \\) (鮒一羽二羽一羽二羽しごく惜しい)

\\(e\\) は自然数の階乗の逆数を合計したものでもあります。

\\[
e = \frac{1}{0!} + \frac{1}{1!} + \frac{1}{2!} + \frac{1}{3!} + \frac{1}{4!} + \cdots
\\]

---

## 定義


ネイピア数の定義は下記2通りある。

\\[
e = \lim_{n \to \infty} \left( 1 + \frac{1}{n} \right) ^ n
\\]

もしくは

\\[
e=\lim_{h \to 0} ( 1 + h ) ^ {\frac{1}{h}}
\\]

---

## 重要な性質

ネイピア数「 \\(e\\) 」を底とする指数関数 \\(y=e^x\\) は重要で、\\(e^x\\) は微分しても積分しても \\(e^x\\) となり、何回微積分しても変わらない唯一の関数としても知られる。
どちらかというと、そうなるような値を求めて \\(e\\) としたと考えた方がいい。

「何回微積分しても変わらない」というのは、意味合い的には加法(足し算)においては \\(0\\) 、
乗法(掛け算)においては \\(1\\) で、どちらも相手を変化させないですよね。数学ではこういう値が重要なのです。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[自然対数の底(ネイピア数) e は何に使うのか - Qiita](https://qiita.com/yaju/items/093854baa667a40f9e04)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


## ネイピア数の指数関数と対数関数の変換

\\[ 
\begin{eqnarray}
C  &=& e^{C'}     \tag{1}\label{ex_log1} \\\\\
C' &=& \log_{e} C \tag{2}\label{ex_log2}
\end{eqnarray}
\\]

指数関数と対数関数の変換から、 \\( \eqref{ex_log1} \\) を \\( \eqref{ex_log2} \\) の形に変換

\\[
e^{C'} \Longrightarrow e^{log_{e}C} \tag{3}\label{ex_log3}
\\]

\\( \eqref{ex_log3} \\) で \\( \eqref{ex_log1} \\) の \\( C' \\) に \\( \eqref{ex_log2} \\) を代入

\\[
\Longrightarrow C = e^{log_{e} C} = \exp(\log C) \tag{4}\label{ex_log4}
\\]

結果、 \\( \eqref{ex_log4} \\) のように書くことができる。



## ネイピア数関係の関数表記

\\[
\begin{eqnarray}
  \log_{e} x &\Longleftrightarrow& \ln x   \tag{1}\label{e_ln} \\\\\
         e^x &\Longleftrightarrow& \exp(x) \tag{2}\label{e_exp}
\end{eqnarray}
\\]

\\(  \eqref{e_ln} \\) 、 \\( \eqref{e_exp} \\) ともに左右は同じ意味になる。 
プログラミング言語では、一般に \\( \eqref{e_ln}\\) 、 \\( \eqref{e_exp} \\) を

\\[
\begin{eqnarray}
  \log ( x ) \\\\\\\\
  \exp ( x )
\end{eqnarray}
\\]

と表記する。

### 読み方・由来

#### \\( ln(x) \\)

{% capture text %}
\\( \ln \\) はラテン語の「logarithmus naturalis」の略らしいです（自信はないです）。
私はずっと「log natural」の頭文字だと思っていました。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[数学記号exp，ln，lgの意味 \| 高校数学の美しい物語](https://mathtrain.jp/explnlg)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}


読み
: 自然対数
: エルエヌ

由来
: {% include cite.html text=text source=source %}

例
: \\( \ln(x) \\)
    : エルエヌ エックス
    : x の自然対数


#### \\( exp(x) \\)

読み
: イーエクスピー
: 自然指数関数
: 指数関数

由来
: exponential 
    : 指数関数的な

例
: \\( \exp(x) \\)
    : イーエクスピー エックス
    : 自然指数関数 x 


