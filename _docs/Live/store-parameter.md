---
layout : post
title  : "Max for Live: 前回利用したパラメータが保存され、読み込まれるようにする"
date   : 2017/04/02
lastchange : 2017-06-07 21:45:40.
tags   :
  - Max for Live
  - parameter
  - paraminspector
  - save
  - state

---

## 昨日いい感じに調整したパラメータが保存されてなくて困った

Max for Live のデフォルトの挙動ではLive セットを保存した時に
Max for Liveのパラメータは保存されないらしい。

Max for Liveのパラメータが残る方法を調べた。
なお、リファレンスの整備が整っていない部分らしい。

参考

* [Max 7 - Parameter attributes in Max](https://docs.cycling74.com/max7/vignettes/parameter_enable)
* [Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)

## Parameter インスペクタを利用する

> In order to provide interoperability between the Max and Live application,
> Max for Live introduced some new attributes intended to allow users to include generic Max UI objects
> (e.g. [rslider](https://docs.cycling74.com/max7/maxobject/rslider),
> [multislider](https://docs.cycling74.com/max7/maxobject/multislider) ) as part of a Max for Live device. 
> These parameter attributes are available to all Max users,
> and provide a simple means for saving the initial state of any Max/Max for Live UI object
> in a Max patch in addition to [their use with Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters). 
> 
> ---
> 
> * [Max 7 - Parameter attributes in Max](https://docs.cycling74.com/max7/vignettes/parameter_enable)

> Parameters are settings of Max for Live devices you want to store and/or automate in Live.
> In some cases, a parameter may be set once and never change. In other cases,
> you'll want to use Max objects to interact with parameter values by clicking and moving the mouse,
> by receiving MIDI data mapped to a parameter, or via Live [automation](https://docs.cycling74.com/max7/vignettes/live_automation).
> 
> There are two ways to add parameters to your device:
> using the Live UI objects or using [pattr](https://docs.cycling74.com/max7/maxobject/pattr) objects with
> [parameter mode enabled](https://docs.cycling74.com/max7/vignettes/live_pattr#parameter_mode_enable).
> 
> The [Parameters Window](https://docs.cycling74.com/max7/vignettes/live_parameterswindow) shows
> all parameters currently associated with a device,
> and permits you to change parameter attributes in a single place.
> You can also change parameter attributes for individual objects by using the Parameter tab of the 
> [Inspector](https://docs.cycling74.com/max7/vignettes/inspector). 
> 
> ---
> 
> * [Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/store-parameter/01_parameter.png %}{% endcapture %}
{% assign caption = '
* [Max 7 - Parameter attributes in Max](https://docs.cycling74.com/max7/vignettes/parameter_enable)

Parameter インスペクタ
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


* Max for Live/Max の UIオブジェクトには*Parameter* というインスペクタがある？
* *Parameter* インスペクタを有効にすれば state(状態) がLiveセットに store(保存) される？
* Max UI オブジェクトの *Parameter* を保存するには `pattr` オブジェクトと接続して、
`pattr`の *Parameter* インスペクタを有効にする必要がある？

・・・やってみる。
{% capture url %}{{ site.github.url }}{% link _docs/Live/images/store-parameter/02_patch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    600.3oc0VssiaBCD8Y3q.wyoQ1bO8WY0pHSvIwq.ajwPypU4eu9BlKcYyMso
    UEILxyLdlwGelw7gqieN6Dtw26mdu3437gqiiVjRfS+bG+JzocknFsY9T7uX
    4u4uxnpCwonJrVSaVF..h.oIVsB7IgVUMRH3VojBsLoW9Q.vJj1VwZEkXgNJ
    g8Rq43FLUfDDFcKGuSXxzvfMqAq7BSTif9AuW6WTi38RSFYctwyh2qwl0KUz
    +NrHY7ITa3g1viD6NRnGlD5fDvXnifpw.3jfKSXAi2GmjnooEpCWrUtqk9Zq
    BOH4sBC16Lf0N9HJk0ugGP198gJg3RYBLeKlhxM6RnV2Y2YgYv+WHRcnxVLa
    uUrU9znTxnG95y2Yl1bjwEi1FGqLNIKcIasmD.ipyttiezixcyMRG2KSw1pk
    niRw4X9KAutDuCtLuKX3f+y3L3JbxHMwHMZQN4dFuBosL49Xo4H5gGfklBGS
    lXvHK8AP3KUvGmnHDwY2bAObykK3ep0tvzv0gxmfras.Fb+0uKRcdd76kv3j
    6mbC+2vS2jtNQ9jFrDW8o2PaFBdglYescVPHbAcDZGogLqI8ysSGboNcvfGu
    S2eYx.LKZn77aoy0bDaBnD++Clb893Ze5WRn+4+wocmR9bHpg0x2YyL6Og4A
    GRsBYCPBUe81Tij2X5MVTdjTTfoSAlBRiBqJF528oCqaMeT2Qb07Qwqen7w.
    Wn55NLuo2m5TQxcdiwUSSWomRnlo5ha48BpxYi8ZhhOhKOsDxipVy0E9mRh7
    MKkUf4zVRekuLxmc+MvVvHaX
    -----------end_max5_patcher-----------

左から

1. `flonum`の *Parameter* 操作
    1. *Parameter* 無効
    2. *Parameter* 有効
2. `pattr`の *Parameter* 操作(`flonum`の *Parameter* 無効)
    1. *Parameter* 無効
    2. *Parameter* 有効

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/store-parameter/03_before.png %}{% endcapture %}
{% assign caption = '雑に動かして Live セットを保存する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/store-parameter/04_after.png %}{% endcapture %}
{% assign caption = '
Live セットを再読込する。

1. `flonum`の *Parameter* 操作
    1. 0に戻った
    2. 保存した時の状態が復元された
2. `pattr`の *Parameter* 操作
    1. 0に戻った
    2. 保存した時の状態が復元された

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`flonum`/`pattr`問わず *Parameter* を有効にしていると前回保存した時点の状態を呼び出せる。

そしてこれはMaxパッチでも同様らしい。Maxでイケてるパラメータを保持しておきたいときに利用できる。
