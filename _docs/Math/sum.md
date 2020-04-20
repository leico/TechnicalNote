---
layout : post
title  : 総和について
date   : 2020/04/20
lastchange : 2020-04-20 23:55:59.
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


