---
layout : post
title  : 数学の数式を色々まとめてみる
date   : 2020/04/18
lastchange : 2020-04-19 00:22:12.
tags   :
  - math
  - log
  - exponential
  - sum
  - sigma
---

## 公式を調べたり、読み解いてみたり、証明してみたりをまとめる

### 指数関数

\\[
y = x^a
\\]

の形式で表される関数

### 対数関数

\\[
y = log_{a}x
\\]

の形式で表される関数


### 指数関数と対数関数の変換

\\[
a^p = M \Longleftrightarrow p = \log{a}M
\\]

指数関数 \\(a^p = M\\) の指数 \\(p\\) は \\(a\\) を底とする対数関数 \\(\log{a}M\\) の解として求めることができる。


### 自然対数の底(ネイピア数)

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


#### ネイピア数の指数関数と対数関数の変換 [^1]

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



#### ネイピア数関係の関数表記

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



### 総和に乗算する [^2]

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


### 活性化関数

#### シグモイド関数 [^3]

\\[
h(x) = \frac{1}{1 + \exp(-x)}
\\]

#### ステップ関数 [^4]

\\[
\begin{eqnarray}
  h(x) = 
  \begin{cases}
    1 & (x \gt 0) \\\\\\
    0 & (x \leqq 0)
  \end{cases}
\end{eqnarray}
\\]

#### ReLU関数 [^5]

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

#### ソフトマックス関数 [^6]

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


#### ソフトマックス関数のオーバーフローを防止する [^7]

\\[
\begin{eqnarray}
  \sigma(k) &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \quad (1 \leqq k \leqq n)                      \\\\\[10pt]
            &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \times 1                   \tag{1}\label{of_1} \\\\\[10pt]
            &=& \dfrac{\exp(a_k)}{\displaystyle \sum_{i = 1}^n \exp(a_i) } \times \frac{C}{C}         \tag{2}\label{of_2}
\end{eqnarray}
\\]

\\( \eqref{of_2} \\) は \\( \eqref{of_1} \\) の \\( 1 \\) を置き換えている。 \\( C \\) は

\\[
-\max(a[k]) \quad (1 \leqq k \leqq n) 
\\]

を用いるのが一般的。


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


### 損失関数

#### 二乗和誤差 [^8]

\\[
E = \frac{1}{2} \sum_{k} (y_k - t_k)^2
\\]

\\( y \\)
: ニューラルネットワークの出力

\\( t \\)
: 教師データ

\\( k \\)
: 次元数(走査対象配列の、全ての要素の総和)

#### 二乗和誤差と one-hot 表現 [^9]


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


#### 交差エントロピー誤差 [^10]

\\[
E = - \sum_{k} t_k \log_{e} y_k
\\]

\\( y \\)
: ニューラルネットワークの出力

\\( t \\)
: 教師データ

\\( k \\)
: 次元数(走査対象配の、全ての要素の総和)


#### 交差エントロピー誤差と one-hot 表現[^11]

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

[^1]: 斎藤康毅 「ゼロから作るDeep Learning ---Pythonで学ぶディープラーニングの理論と実装」, オライリー・ジャパン,2016年, P68
[^2]: 同, P68
[^3]: 同, P45
[^4]: 同, P45
[^5]: 同, P53
[^6]: 同, P66
[^7]: 同, P69
[^8]: 同, P88
[^9]: 同, P88
[^10]: 同, P89
[^11]: 同, P89
