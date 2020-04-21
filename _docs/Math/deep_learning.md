---
layout : post
title  : 機械学習関係の数式を色々まとめてみる
date   : 2020/04/18
lastchange : 2020-04-21 18:06:57.
tags   :
  - math
  - log
  - exponential
  - sum
  - sigma
---

## 公式を調べたり、読み解いてみたり、証明してみたりをまとめる

### 指数関数、対数関数

指数関数は \\( y = a^x \\) 、対数関数は \\( y = \log_{a} x \\) のような形をしている。

[指数、対数について]({{site.github.url}}{% link _docs/Math/log_exp.md %})




### 自然対数の底、ネイピア数

\\( y = e^x \\) 、 \\( y = log_{e} x \\) のように出てくる、 \\( e\\) という \\( \pi \\) のような定数。

[自然対数の底について]({{site.github.url}}{% link _docs/Math/naipier.md %})



### 総和

\\(\displaystyle \sum_{i = 1}^{n} x_i \\) のような形をした数式について。

[総和について]({{site.github.url}}{% link _docs/Math/naipier.md %})


### 活性化関数

#### シグモイド関数 [^sigmoid]

\\[
h(x) = \frac{1}{1 + \exp(-x)}
\\]

#### ステップ関数 [^step]

\\[
\begin{eqnarray}
  h(x) = 
  \begin{cases}
    1 & (x \gt 0) \\\\\\
    0 & (x \leqq 0)
  \end{cases}
\end{eqnarray}
\\]

#### ReLU関数 [^relu]

ランプ関数というらしい

\\[
\begin{eqnarray}
  h(x) = 
  \begin{cases}
    x & (x \gt 0) \\\\\\
    0 & (x \leqq 0)
  \end{cases}
\end{eqnarray}
\\]

### 出力層の関数

#### ソフトマックス関数 [^softmax]

\\[
\begin{array}{ll}
\sigma (k) = \dfrac{ \exp (a_k) }{ \displaystyle \sum_{i = 1}^n \exp (a_i) } & (1 \leqq k \leqq n) \tag{1}\label{sm_1}
\end{array}
\\]

\\( \sum \\) を展開する

\\[
\sum_{i = 1}^n \exp(a_i) = \exp(a_1) + \exp(a_2) + \exp(a_3) + \cdots + \exp(a_n) \tag{2}\label{sm_2}
\\]

\\( \eqref{sm_2} \\) から、 \\( \eqref{sm_1} \\) の分母は \\( e \\) を出力された値で累乗したものの総和になる。

次に \\( k \\) を順番に見ていくと

\\[
\begin{eqnarray}
  \sigma (1) &=& \dfrac{ \exp(a_1) }{ \exp(a_1) + \exp(a_2) + \exp(a_3) + \cdots + \exp(a_n) } \\\\\[10pt]
  \sigma (2) &=& \dfrac{ \exp(a_2) }{ \exp(a_1) + \exp(a_2) + \exp(a_3) + \cdots + \exp(a_n) } \\\\\[10pt]
  \sigma (3) &=& \dfrac{ \exp(a_3) }{ \exp(a_1) + \exp(a_2) + \exp(a_3) + \cdots + \exp(a_n) } \\\\\[10pt]
  &\vdots& \\\\\[10pt]
  \sigma (n) &=& \dfrac{ \exp(a_n) }{ \exp(a_1) + \exp(a_2) + \exp(a_3) + \cdots + \exp(a_n) } 
\end{eqnarray}
\\]

なので、\\( \sigma(k) \\) は \\( e \\) の累乗の総和における、 \\( \exp(k) = e^k \\) の割合を出力する。
割合なので、\\( \sigma(k) \\) の値は

\\[
0 \leqq \sigma(k) \leqq 1
\\]

また、

\\[
\begin{eqnarray}
  \sum_{k=1}^n \sigma(k) = \sigma(1) + \sigma(2) + \sigma(3) + \cdots + \sigma(n)
                         = 1
\end{eqnarray}
\\]

となる。


#### ソフトマックス関数のオーバーフローを防止する [^softmax_overflow]

\\[
\begin{eqnarray}
  \sigma(k) &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \quad (1 \leqq k \leqq n)                      \\\\\[10pt]
            &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \times 1                   \tag{1}\label{of_1} \\\\\[10pt]
            &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \times \frac{C}{C}         \tag{2}\label{of_2}
\end{eqnarray}
\\]

\\( \eqref{of_2} \\) は \\( \eqref{of_1} \\) の \\( 1 \\) を置き換えている。 

\\[
\begin{eqnarray}
  &=& \dfrac{ C \exp(a_k) }{C \displaystyle \sum_{i = 1}^n \exp(a_i) }                                    \tag{3}\label{of_3} \\\\\[10pt]
  &=& \dfrac{ \exp( \log_{e} C ) \exp(a_k) }{ \exp( \log_{e} C ) \displaystyle \sum_{i = 1}^n \exp(a_i) } \tag{4}\label{of_4}
\end{eqnarray}
\\]

\\( \eqref{of_4} \\) で \\( C \\) をネイピア数の累乗の形 \\( e^x = \exp(x) \\) に変形させている。

\\( a^x \times a^y = a^{(x + y)} \\) 、\\( A \sum x = \sum Ax \\) なので

\\[
\begin{eqnarray}
  &=& \dfrac{ \exp(a_k + \log_{e} C ) }{ \displaystyle \sum_{i = 1}^n \exp(\log_{e} C )\exp(a_i) }        \tag{5}\label{of_5} \\\\\[10pt]
  &=& \dfrac{ \exp(a_k + \log_{e} C ) }{ \displaystyle \sum_{i = 1}^n \exp(a_i + \log_{e} C) }            \tag{6}\label{of_6}
\end{eqnarray}
\\]

\\( \log_{e} C = C' \\) と置き換えると

\\[
= \dfrac{ \exp(a_k + C' ) }{ \displaystyle \sum_{i = 1}^n \exp(a_i + C' ) }                           \tag{7}\label{of_7} 
\\]

\\( C' \\) は

\\[
-\max(a[k]) \quad (1 \leqq k \leqq n) 
\\]

を用いるのが一般的。




### 損失関数

#### 二乗和誤差 [^mean_square]

\\[
E = \frac{1}{2} \sum_{k} (y_k - t_k)^2
\\]

\\( y \\)
: ニューラルネットワークの出力

\\( t \\)
: 教師データ

\\( k \\)
: 次元数(走査対象配列の、全ての要素の総和)

#### 二乗和誤差と one-hot 表現 [^mean_square_onehot]


\\[
E = \frac{1}{2} \sum_{k} (y_k - t_k)^2
\\]

\\( t = 0 \\) の誤差計算は、

\\[
\begin{eqnarray}
E_0 &=& \frac{1}{2} \sum_{k, t_k = 0} (y_k - 0)^2 \\\\\[10pt]
    &=& \frac{1}{2} \sum_{k, t_k = 0} {y_k}^2     \tag{1}\label{ms_hot1}
\end{eqnarray}
\\]

\\( \eqref{ms_hot1} \\) から、不正解確率の総和 の半分

\\( t = 1 \\) の出力の計算は

\\[
E_1 = \frac{1}{2} \sum_{t_k = 1} (y_k - 1)^2
\\]

one-hot 表現なので、 \\( t = 1 \\) となる要素は1つしかない。よって

\\[
E_1 = \frac{1}{2} (y_k - 1)^2 \tag{2}\label{ms_hot2}
\\]

\\( \eqref{ms_hot2} \\) より、正解からの誤差の2乗の半分


#### 交差エントロピー誤差 [^cross_entropy]

\\[
E = - \sum_{k} t_k \log_{e} y_k
\\]

\\( y \\)
: ニューラルネットワークの出力

\\( t \\)
: 教師データ

\\( k \\)
: 次元数(走査対象配の、全ての要素の総和)


#### 交差エントロピー誤差と one-hot 表現[^cross_entropy_onehot]

\\[
E = - \sum_{k} t_k \log_{e} y_k
\\]

\\( t = 0 \\) の誤差計算は、

\\[
\begin{eqnarray}
E_0 &=& - \sum_{k, t_k = 0} 0 \times \log_{e} y_k \\\\\[10pt]
    &=& - \sum_{k, t_k = 0} 0                     \\\\\[10pt]
    &=& 0       \tag{1}\label{ce_hot1}
\end{eqnarray}
\\]

\\( \eqref{ce_hot1} \\) より、不正解の影響は受けない。

\\( t = 1 \\) の誤差計算は、

\\[
E_1 = - \sum_{k, t_k = 1} 1 \times \log_{e} y_k
\\]

one-hot 表現なので、 \\( t = 1 \\) となる要素は1つしかない。よって

\\[
E_1 = - \log_{e} y_k \tag{2}\label{ce_hot2}
\\]

\\( \eqref{ce_hot2} \\) より、教師データからの乖離によって誤差を計算する。





---

[^sigmoid]: 斎藤康毅 「ゼロから作るDeep Learning ---Pythonで学ぶディープラーニングの理論と実装」, オライリー・ジャパン,2016年, P45
[^step]: 同, P45
[^relu]: 同, P53
[^softmax]: 同, P66
[^softmax_overflow]:同, P69
[^mean_square]: 同, P88
[^mean_square_onehot]: 同, P88
[^cross_entropy]: 同, P89
[^cross_entropy_onehot]: 同, P89
