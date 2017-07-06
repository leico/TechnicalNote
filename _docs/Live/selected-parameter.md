---
layout : post
title  : "selected_parameterを監視していると誤ったidが出力されることがある"
date   : 2017/07/06
lastchange : 2017-07-06 22:23:49.
tags   :
  - Max for Live
  - live.observer
  - selected_parameter
---

## 選択しているパラメータを取得できる *selected_parameter* 

[Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)内の、
*live_set view selected_parameter* を `live.observer` で監視していると、Liveで選択したオブジェクトを取得することができる。

{% capture text %}

##### selected_parameter

Type
: [DeviceParameter](https://docs.cycling74.com/max7/vignettes/live_object_model#DeviceParameter)

Access
: get, observe

Description
: The selected parameter, or "id 0"

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

実際に *id* と *path* を取得してMaxウィンドウに表示するパッチは以下。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/selected-parameter/01_selected-parameter.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    649.3ocyW9sbhCBEF+53SASt1sSHQq59Xza2YGGL4TKchPFBIUmN8cegCIsc
    U1QTSb1aHlC+4ieeANfuOIJdibOTGS9I4Wjnn2mDEggrAh5dOJdGaedIqFaV
    r.dSt403otpzvdMFtRwEZRES+ReU7BrBSi+AcdePQyNtnDz3XQ+JnrQ2GMoK
    Zs9PIfCQemMid9Kbw10JHW6lyYIIOjLkPoKsOVPskolRxusc4iISrESGBzL3
    3ArzwCrGsrjgkyWMFbU.OCpR4a93hdAbQuDtnqPVnoKOFrt93FX8gJv0g336
    DyKGYjO8S4cgXMYKns6KIk99PO6BnN8JndQFVtH.pmNvjWxagGLUXmRdHOyK
    4oC2R7DZvn+YU0rVnXsaRulo0J9lFsKAczmzGEutBT07ZMHxwwfhwQaanLOI
    qXCSr0mykLV6TRwsHAmY.meCaldnBDEjJkzXv5Cdg+w9nOKEZAamipm345Cj
    mfsMkL035OzjtGKt6oRpgRyjwrBshoLjqAk+rJIoCpIkdEKhb2KXV148nSgZ
    rREUCpVP4wvVs3J8qzgxunYyC1v5rm+6RYYMY2IclestFzjVN36n9UYC5xyr
    K2tcWFfNaUft8IKHw1aPVb7s2QIsw+aCrV1nx6Gx9kbjujs.p0bASykhu0nk
    t138aTvBYLaB8LBgYLtYoVF.R1a77sFIUElcj8qGGWkS8qbxMo7Q77OjNa.b
    WTIZHJQuUkxBgI5PvDMDklODJYu3vYkpeW4MJUZ.eoPonChTm0.6Y+F0JDC7
    zU5tTkrppV6IPtVihXNU4UItEbwT7Utv8JlPOVAs791OCivTlr4ZSp7FkKO+
    9Gc++o3cRylYQCuKShQ4Ol7G8Fg.cB
    -----------end_max5_patcher-----------

*id* と *path* を取得してMaxウィンドウに表示するパッチ
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/selected-parameter/02_id-path.png %}{% endcapture %}
{% assign caption = '
*id* と *path* を表示した例
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

実際に表示してみると

* パラメータ以外を選択すると *id 0* が出力される
* Max for Liveデバイスのパラメータを選択する際、何故かほぼ同時に異なる *id* が出力されることがある

ということがわかった。これらを解決する。
が、そのうちに Cycling' 74 が修正してくれることを願う。


## *id 0* を除外し、正しい *id* のみ出力するようにする

まずは完成形。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/selected-parameter/03_fixpatch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    836.3ocyX1sjZBCEG+Z7oHCWa2gDPA5iwdamNNHbVM6fAlPzU6N66dCIPc+H
    SMnI0dwJKgX9memuxQdcVP35liPWH56nefBBdcVPfZn9ABFtOHbWwwx5hN0z
    BYvKMqeNbt9QB3nPMbKmxDnZZmX7QzJ0CjS9ajzwAY62QY0fPsV3yC1rWLNZ
    zvnchS0fZIF+xsEhxsT1lUbnTn2ywQjGhlivwpKo39OIxOQ+r+q71rY8eL+1
    P6W0O.kz5ND1DZXinQLhFYRnkE2Syh7OS1vWQuthSsfd9x0R9mKAWrkCcaQQ
    FvFmNArwWgGkPNC+Ev1kH2xgVfUgjnZ.5kSHLFeE95L680tD5xsErMfQ+LIZ
    BHGOEjIjDUlKdg0NZY7srHy3EWZA3RwLa.v49J+dv.jo71KhuC42ZpMGpm4Y
    rWDOE2tiyw6OpRt01ZB7E95npjk4d+nJMZl8nDucFrt30.Xu2k5Nvpfm.dcy
    Kl.C6qpxCdrrk2kih9KHm4KhGRNuSG9JPa.QedIp1jeNYBTStB+brJkLM8NT
    Rpld.dP9f9sjAxi8UyVCjOziYpU96w0u3.TsRumWUHDb5Z4oIcCrO.eP3pVf
    2I+UA.qTsFX03JqlqrcMEUqkMvXxvE4sF1T0DxsMMQs+7Qeps7FoAVbxH7+o
    c0mZXBVwNMUORKEmPOBa1WWv8q8AGk9tK+SqjzA0xMiLBssfKIW.byEUhHN0
    HQltQR2.HNwhN.+JT9pRTGvO.bCFr7zqzdQbk8BObIw9Vl+uqjUuQVePm7+V
    0ABzAJX5j97XmFdFOcycrNENw1N0+R.oZ9RjYe98Lojre7OZ.6Z1yKGWxwPN
    zYYqfNAkUHnMr2MoL8bL5irVHowFgufPpJF2rTYVfD1EL0+9otLSKcgRQ1vT
    pCTp+2jeQlF2N2lRV4mxcgR1DjS93jZ3UxZziUn7rzXyRGcaRuzF6ax8SZhW
    L3I1HcrKhpRrI6enr2soTrMLgcASXaTZgKTJxlXjbmTRScpB1FovNQpKZ.iV
    5Drrw.90HccOCEssG5aESOakHx1qdtQkBlNWcKkouU0YSHGNPGmehZjBtrsF
    grml8bcCOGWpeOBg6ZjIyr8zg7YoxuM62TCbtjJ
    -----------end_max5_patcher-----------

修正を加えてとても見づらくなったパッチ
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




### *id 0* を除外する

1. `route id`の左アウトを使って *id xxx* というメッセージのみを通過させつつ、 *id* の接頭辞を除外
2. `route 0`の右アウトを使って *0* 以外の *id* を通過させる

ここまでの処理によって *id* の数値のみが出てくる。
処理がしやすくなったところで`change`オブジェクトを使い、同じ *id* が出力されないようにする。

### 正しい *id* のみを出力する

ほぼ同時に2つの *id* が出力されていたので、まず`thresh 0`で出力を1つのリストにできるか試したところ、
リストになったのでリストを処理して正しい *id* のみを通過させる。

観察してみると2つ目に出てくる *id* が正しいと判明。というか、後々に出てくる *id* がおそらく正しい値になっているのだろう。
なのでリスト最後の *id* だけ出力させる。リスト最後から1つ目だけを切り出すために`zl.ecils 1`を用いている。
**ecils** は **slice** の逆読み。

右アウトから出力される 正しい *id* に、接頭辞 **id** を`prepend id`で付け足して完成。

