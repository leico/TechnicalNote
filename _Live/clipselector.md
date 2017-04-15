---
layout : post
title  : "ClipSelector"
date   : 2017/04/06
lastchange : 2017-04-15 18:42:26.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - Device
  - ClipSelector
---

## 概要

{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/01_Detail.png %}{% endcapture %}
{% assign caption = '外観' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

いずれかのクリップスロットを選択、Launch/Record するためのデバイス。
MIDIコントローラのボタンやノブ、フェーダをアサインして利用することを想定したもの。

{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/02_Source.png %}{% endcapture %}
{% assign caption = '全体像

ダウンロードはこちらから

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## 構造

Ableton Live のクリップスロットは`トラック数 x シーン数` のマトリクス(行列)で構成されている。

{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/03_Clipslot.png %}{% endcapture %}
{% assign caption = 'トラック数 x シーン数' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

クリップスロットはトラック側からか、シーン側からアクセスすることになり、
それぞれ`live_set scenes N clip_slots M`、`live_set tracks N clip_slots M`という *path* を利用することになる
(N、Mの値は0 - トラック数orシーン数)。

トラック数、及びシーン数は Live 実行中に変動するため、最大トラック数、シーン数を監視しておく必要がある。

{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/04_Selector.png %}{% endcapture %}
{% assign caption = '
監視する部分のパッチ

`live.observer`で *tracks* / *scenes* を監視、
全てのトラック or シーンに対応する *id* がリストで出力されるので、
その総数をカウントし、`live.number`の最大値に指定する。

*id* のリストは

    id 1 id 2 id 32 id 43 id 55 ...

となっている。今回は`zl.filter id` でリスト内からシンボル`id` を除外し、
`id` が取り除かれたリスト要素の総数を`zl.len`で出力し、トラック数/シーン数を求めている。

他に、`live.observer`の出力を`zl.len`に入力、出力される総数を`/ 2`する方法も可能。
`id N`が総数分出力されるため、リスト長は必ず最大数の2倍になるため。

16トラックの場合、0-15の値を指定することができる。そのままでは1つ多いので最後に`- 1`を行っている。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/05_Path.png %}{% endcapture %}
{% assign caption = '
`live.number`に入力された数値を *path* へ変換し、`live.path`へ入力、
割り当てられた *id* を取得する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{% link _Live/images/clipselector/06_Select.png %}{% endcapture %}
{% assign caption = '

指定されたクリップを選択状態にし、再生/録音/停止が送信できるよう準備する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

