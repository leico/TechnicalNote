---
layout : post
title  : 微分について
date   : 2020/04/20
lastchange : 2020-04-21 03:18:34.
tags   :
  - math
  - derivative
  - 微分
  - dx
  - dy
---

## 考え方

関数 \\( y = f(x) \\) で \\( x \\) がごくわずか変化した時の \\( y \\) の変化量を求める。

微分で考えるのはつねに、ある変数が変化した際の解の変化量である。
関数 \\( y = f(x) \\) 解の変化量に規則性があると、それを関数の形で表すことができるようになる。この関数を

\\[
f'(x) 
\\]

と表す。微分前の関数 \\( y = f(x) \\) の解と変数の関係をより明示的に示す場合

\\[
\frac{dy}{dx} \tag{1}\label{der_1}
\\]

と書く。 \\( \eqref{der_1} \\) を \\( y \\) を \\( x \\) で微分したと表す。

解の変化量は2点間の、解と変数の差で求めることができる。

\\[
\begin{array}{ccc}
\frac{ \text{解の変化量} }{ \text{変数の変化量} } & = &
\frac{ \text{地点 1 の解} - \text{地点 2 の解} }{ \text{地点 1 の値} - \text{地点 2 の値} } \tag{2}\label{der_2}
\end{array}
\\]

\\( \eqref{der_2} \\) の左辺は \\( \eqref{der_1} \\) ととても似ている形をしているのが分かる。
解、変数それぞれのごくわずかな変化量を求めているのが \\( \displaystyle \frac{dy}{dx} \\) でもある。

## 読み方・由来

### \\( f'(x) \\)


{% capture text %}
現代、最も広く用いられる微分の現代的記法のひとつは[ジョゼフ＝ルイ・ラグランジュ][joseph]
により提唱された[プライム][prime]記号（ \\('\\) ）を用いた **ラグランジュの記法** である。

[joseph]: https://ja.wikipedia.org/wiki/%E3%82%B8%E3%83%A7%E3%82%BC%E3%83%95%EF%BC%9D%E3%83%AB%E3%82%A4%E3%83%BB%E3%83%A9%E3%82%B0%E3%83%A9%E3%83%B3%E3%82%B8%E3%83%A5
[prime]: https://ja.wikipedia.org/wiki/%E3%83%97%E3%83%A9%E3%82%A4%E3%83%A0

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[微分の記法 - Wikipedia](https://ja.wikipedia.org/wiki/%E5%BE%AE%E5%88%86%E3%81%AE%E8%A8%98%E6%B3%95)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}


読み
: エフダッシュ エックス
: エフエックスの微分

由来
: {% include cite.html text=text source=source %}


例
: \\( f(x) = x^2 \\) \\( f'(x) = 2x \\)
    : 関数 \\( f(x) = x^2 \\) を微分すると \\( f'(x) = 2x \\) になる



### \\( \displaystyle \frac{dy}{dx} \\)

{% capture text %}
\\( \Delta x\\) と \\( \Delta y \\)がそれぞれ \\( x \\) と \\( y \\) の有限微小変化量を表すように
\\( x \\) と \\( y \\) の微小な変化量すなわち無限小変化量を表す記号として \\( dx \\) と \\( dy \\) を用いる。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ライプニッツの記法 - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%A9%E3%82%A4%E3%83%97%E3%83%8B%E3%83%83%E3%83%84%E3%81%AE%E8%A8%98%E6%B3%95)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}


読み
: \\( y \\) を \\( x \\) で微分
: ディワイ ディエックス

由来
: {% include cite.html text=text source=source %}

例
: \\( \displaystyle \frac{dy}{dx} \\)
    : \\( y \\) を \\( x \\) で微分
: \\( \displaystyle \frac{dh}{dx} \\)
    : \\( h \\) を \\( x \\) で微分




### \\( \displaystyle \frac{d}{dx} \\)

読み
:  \\( x \\) で微分
: ディ ディエックス

何かをとりあえず微分する時などに使う。




### \\( \displaystyle \frac{ \partial y}{ \partial x} \\)


{% capture text %}
現代的な偏微分記法は[アドリアン＝マリ・ルジャンドル][adrian] が導入しているが、後が続かなかった。
これを1841年に再導入するのが[カール・グスタフ・ヤコブ・ヤコビ][carl]である。 

[adrian]: https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%89%E3%83%AA%E3%82%A2%E3%83%B3%EF%BC%9D%E3%83%9E%E3%83%AA%E3%83%BB%E3%83%AB%E3%82%B8%E3%83%A3%E3%83%B3%E3%83%89%E3%83%AB
[carl]: https://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%BC%E3%83%AB%E3%83%BB%E3%82%B0%E3%82%B9%E3%82%BF%E3%83%95%E3%83%BB%E3%83%A4%E3%82%B3%E3%83%96%E3%83%BB%E3%83%A4%E3%82%B3%E3%83%93 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[偏微分 - Wikipedia](https://ja.wikipedia.org/wiki/%E5%81%8F%E5%BE%AE%E5%88%86)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}


読み
: \\( y \\) を \\( x \\) で偏微分
: ディワイ ディエックス

由来
: {% include cite.html text=text source=source %}

例
: \\( \displaystyle \frac{ \partial y}{ \partial x} \\)
    : ディワイ ディエックス


複数の変数がある関数の内、1つだけの変数について解の変化量を求める(偏微分)の時に利用する。





## 一次関数で考える

### 変化量をごくわずかに近づける

関数 \\( y = 2x \\) で \\( x = 0 \rightarrow 0.1 \\) の時の \\( dy \\) は

\\[
\frac{dy}{dx} = \frac{y_{0.1} - y_0}{x_{0.1} - x_0} \tag{1}\label{der_linear1}
\\]

\\( \eqref{der_linear1} \\) の \\(y_{0.1} \\) 、 \\( x_{0.1} \\) に \\( x = 0.1 \\) の時の値を、
\\( y_0 \\) 、\\( x_0 \\) に \\( x = 0 \\) の時の値を代入する。

\\[
\begin{eqnarray}
  &=& \frac{0.2 - 0}{0.1 - 0} \\\\\[10pt]
  &=& \frac{0.2}{0.1}         \\\\\[10pt]
  &=& \frac{2}{1}             \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

\\( x = 0 \rightarrow 0.01 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.02 - 0}{0.01 - 0} \\\\\[10pt]
  &=& \frac{0.02}{0.01}         \\\\\[10pt]
  &=& \frac{2}{1}               \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

\\( x = 0 \rightarrow 0.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.002 - 0}{0.001 - 0} \\\\\[10pt]
  &=& \frac{0.002}{0.001}         \\\\\[10pt]
  &=& \frac{2}{1}                 \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

と、変化量をどこまで小さくしていっても \\( x = 0 \\) の時の \\( y \\) の変化量は \\( 2 \\) である事がわかる。

### \\( x \\) の位置を変化させてみる

\\( x = 0.001 \rightarrow 0.002 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.004 - 0.002}{0.002 - 0.001} \\\\\[10pt]
  &=& \frac{0.002}{0.001}                 \\\\\[10pt]
  &=& \frac{2}{1}                         \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

\\( x = 0.017 \rightarrow 0.018 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.036 - 0.034}{0.018 - 0.017} \\\\\[10pt]
  &=& \frac{0.002}{0.001}                 \\\\\[10pt]
  &=& \frac{2}{1}                         \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

\\( x = -1.000 \rightarrow -1.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{-2.002 + 2.000 }{-1.001 + 1.000} \\\\\[10pt]
  &=& \frac{-0.002}{-0.001}                  \\\\\[10pt]
  &=& \frac{2}{1}                            \\\\\[10pt]
  &=& 2
\end{eqnarray}
\\]

どこをとっても \\( y \\) の変化量は \\( 2 \\) 。つまり \\( y = 2x \\) を \\( x \\) について微分、
\\( x \\) に対する \\( y \\) の変化量を求めたら常に \\( 2 \\) となった。
関数の微分を\\( f'(x) \\) とすると、 \\( f'(x) = 2 \\) と表せる。


## 二次関数で考える

### 変化量をごくわずかに近づける

関数 \\( y = x^2\\) で \\( x = 0 \rightarrow 0.1 \\) の時の \\( dy \\) は

\\[
\frac{dy}{dx} = \frac{y_{0.1} - y_0}{x_{0.1} - x_0} \tag{1}\label{der_2exp1}
\\]

\\( \eqref{der_2exp1} \\) の \\(y_{0.1} \\) 、 \\( x_{0.1} \\) に \\( x = 0.1 \\) の時の値を、
\\( y_0 \\) 、\\( x_0 \\) に \\( x = 0 \\) の時の値を代入する。

\\[
\begin{eqnarray}
  &=& \frac{0.01 - 0}{0.1 - 0} \\\\\[10pt]
  &=& \frac{0.01}{0.1}         \\\\\[10pt]
  &=& \frac{0.1}{1}            \\\\\[10pt]
  &=& \frac{1}{10}
\end{eqnarray}
\\]

\\( x = 0 \rightarrow 0.01 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.0001 - 0}{0.01 - 0} \\\\\[10pt]
  &=& \frac{0.0001}{0.01}         \\\\\[10pt]
  &=& \frac{0.01}{1}              \\\\\[10pt]
  &=& \frac{1}{100}
\end{eqnarray}
\\]

\\( x = 0 \rightarrow 0.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{0.000001 - 0}{0.001 - 0} \\\\\[10pt]
  &=& \frac{0.000001}{0.001}         \\\\\[10pt]
  &=& \frac{0.001}{1}                \\\\\[10pt]
  &=& \frac{1}{1000}
\end{eqnarray}
\\]

変数の変化量を限りなく \\( 0 \\) へ近づけていくと、解の値も限りなく \\( 0 \\) へ近づいていくことが分かる。

### \\( x \\) の位置を変化させてみる

\\( x = 1.000 \rightarrow 1.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{1.002001 - 1.000}{1.001 - 1.000} \\\\\[10pt]
  &=& \frac{0.002001}{0.001}                 \\\\\[10pt]
  &=& \frac{2.001}{1}                        \\\\\[10pt]
  &\fallingdotseq& 2
\end{eqnarray}
\\]

\\( x = 2.000 \rightarrow 2.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{4.004001 - 4.000}{2.001 - 2.000} \\\\\[10pt]
  &=& \frac{0.004001}{0.001}                 \\\\\[10pt]
  &=& \frac{4.001}{1}                        \\\\\[10pt]
  &\fallingdotseq& 4
\end{eqnarray}
\\]

\\( x = 3.000 \rightarrow 3.001 \\) の間を見てみる。

\\[
\begin{eqnarray}
  &=& \frac{9.006001 - 9.000 }{3.001 - 3.000} \\\\\[10pt]
  &=& \frac{0.006001}{0.001}                  \\\\\[10pt]
  &=& \frac{6.001}{1}                         \\\\\[10pt]
  &\fallingdotseq& 6
\end{eqnarray}
\\]

\\( x \\) の値に対しての \\( y \\) の変化量を見てみると

\\[
\begin{eqnarray}
x & \rightarrow & y \\\\\
0 & \rightarrow & 0 \\\\\
1 & \rightarrow & 2 \\\\\
2 & \rightarrow & 4 \\\\\
3 & \rightarrow & 6
\end{eqnarray}
\\]

このような関係になっていることが分かる。つまり、 \\( x \\) に対する \\( y \\) の変化量は常に \\( 2x \\) の割合で規則的に増加していくことがわかる。
この関数の微分を \\( f'(x) \\) とすると、 \\( f'(x) = 2x \\) と表すことができる。

