---
layout : post
title  : コントローラーに対数マッピングを行う
date   : 2017/04/19
lastchange : 2017-06-07 21:49:25.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - log
  - logarithmic
---

## ノブの中央で *0 dB* になるようにしたい

ノブにトラックボリュームをアサインした時、

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/01_knob.jpg %}{% endcapture %}
{% assign caption = 'これが理想なんだけど' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/02_0db.jpg %}{% endcapture %}
{% assign caption = 'ここが *0 dB*' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

なのに、何故か真ん中でカチッっという感触がある[Launch Control XL](http://www.h-resolution.com/novation/launchcontrolxl.php)。
やってらんないので真ん中で *0 dB* になるような変換式を考える。

## Live のトラックボリュームの特性

Live のトラックボリュームの値を調べてみた。\\( C \times logR \\) になっていると変換式が求められそうだが・・・

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/03_volume.png %}{% endcapture %}
{% assign caption = '
こちらプロットデータ

| dB  | value    |
| --: | :------- |
|   6 | 1        |
|   0 | 0.85     | 
| - 3 | 0.774942 |
| - 6 | 0.7      |
| - 9 | 0.624942 |
| -12 | 0.55     |
| -15 | 0.474942 |
| -18 | 0.4      |
| -21 | 0.343664 |
| -24 | 0.302414 |
| -27 | 0.268256 |
| -30 | 0.238439 |
| -33 | 0.21163  |
| -36 | 0.187037 |
| -39 | 0.164219 |
| -42 | 0.142882 |
| -45 | 0.122717 |
| -48 | 0.103536 |
| -51 | 0.085236 |
| -54 | 0.067786 |
| -57 | 0.051012 |
| -60 | 0.034624 |
| -63 | 0.021346 |
| -66 | 0.01113  |
| -69 | 0.002376 |


' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


*-20 dB* 辺りを境界にして2つの特性が組み合わさっている。
こりゃ何かをモデリングしているぞ。

めんどくせぇので簡単な式一発で特性変換する。


## *0.5* が *0.85* になるようにする

[Ableton Liveのレベル、Max for Liveでの値]({{site.github.url}}{% link _docs/Live/dbfs.md %})
で確認したようにトラックボリュームは *0.85* で *0 dB* の値になる。
変換しやすいように、まずMIDI信号の値を\\(0 \to 1 \\) に変換する。

そして変換は[対数、指数っぽい関数を考える]({{site.github.url}}{% link _docs/Memo/log-power.md %})
で使った対数っぽい曲線で行う。

*0.5* を何乗すると *0.85* になるのか調べるため\\(log_{0.5}0.85\\)を行うと

\\[
log_{0.5}0.85 = 0.23446525363702
\\]

計算は[対数関数 - 高精度計算サイト](http://keisan.casio.jp/exec/system/1260332465)で行った。

つまりコントローラーの値を *0.234* 乗してあげればノブのだいたい半分のところで *0 dB* になる。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/04_0.234.png %}{% endcapture %}
{% assign caption = '
[http://graph.tk/#y=x^0.234](http://graph.tk/#y=x^0.234)
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## *0.5* が *0.921* になるようにする

同様に`live.gain~`についても変換式を求める。`live.gain~`の値についても
[Ableton Liveのレベル、Max for Liveでの値]({{site.github.url}}{% link _docs/Live/dbfs.md %})
で書いてある通り。

*0.5* を何乗すると *0.921* になるのか調べるため\\(log_{0.5}0.0921\\)を行うと

\\[
log_{0.5}0.85 = 0.11872693857075
\\]

計算は[対数関数 - 高精度計算サイト](http://keisan.casio.jp/exec/system/1260332465)で行った。

つまりコントローラーの値を およそ *0.12* 乗してあげればノブのだいたい半分のところで *0 dB* になる。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/05_0.118.png %}{% endcapture %}
{% assign caption = '
[http://graph.tk/#y=x^0.118](http://graph.tk/#y=x^0.234)
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## で、使いやすい？

12時で *0 dB* になったのはよい。

しかし低い方の解像度が128段だと全然足りない。微妙。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/knob-mapping/06_-6db.jpg %}{% endcapture %}
{% assign caption = '
この位置で*-6 dB*。本当は10時くらいにきてほしい
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


