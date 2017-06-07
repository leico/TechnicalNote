---
layout : post
title  : "Max for Live: 初期化、ロードのタイミング"
date   : 2017/04/02
lastchange : 2017-06-07 21:44:38.
tags   :
  - Max for Live
  - init
  - initialize
  - live.thisdevice
  - loadbang

---

## *Parameter* インスペクタと`live.thisdevice`、どっちが早い？

初期値を与える方法として、`live.thisdevice`が
ロード完了後に出力する *bang* を利用するというのも考えられる。

で、 *Parameter* インスペクタと `live.thisdevice` の *bang* 、
どっちが先に実行されるのかを実験する。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initial-timing/01_timing.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    477.3ocqTEsiaBCD7Y3q.4moQPHoMp+JUUQF7dI9DwFYuvkSmx+9YVamD5As
    Q20Wv3cGVlcl09szDVs9LXYY+L6WYIIukljPgFCjD1mvNwO2zxsDLVqb.VIj
    7VVtO6.2n3mf4Sp6wV.wW6.++fwxyXO0p4HK62ALp9SRkCEU+xPvNN1bTpNr
    2.Mn+a2VtpHOqrnXbYylwmaqVUbsNcbiiGHX1CJdcKLobFvBJjiRs5tRRkZo
    543km9DwVGhJETmpqe9akUw1DMRt5vzeokO.h8bzkqtGgauYCpaPdGUv1dP+
    TLbL98cTqVcXQUdBR6QsAeLnQaoXlbmblN0MqlKqTIQWQ+nPOGpnTuKprS.0
    6.YwWCUwm+RZ5sE54kz7Gb9TAu37lqFCblrZF5Hv1EmJ+RSja2QCPz6qKWb.
    pblAn0QFcUAXr+e8LY93QoU.CxFXwtu1M6NdtTpv3xm8v4N+Qz+kVT8Qsn5u
    KETgbcj5Oush3xX7o5iU2aZhMXTqytwGAXQohtO3dPUS.cTJDf5tiHLgzNNw
    SztXVm5Q4S0iPm0eR53UKdW2.XrgZRLwM27r1Lt8G4zVoxukbDlwMnDwSW3w
    3FmSiNat23MkyeeCy+oZAXT8xv4V2e9R56PwmTXI
    -----------end_max5_patcher-----------
実験パッチ

`live.thisdevice`からの *bang* を *0.5* に変換して`live.dial`へ入力している。
一方で、`live.dial`は *0.8* で初期化されるように設定してある。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`live.dial`が*0.8* なら *Parameter*の方が後に実行されたことになる。

一方 *0.5* なら `live.thisdevice`の方が後に実行されたことになる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initial-timing/02_result.png %}{% endcapture %}
{% assign caption = '右のデバイスが変更後にロードしたもの' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どうやら`live.thisdevice`のほうが初期化より後に実行される。

## 保存されたパラメータのロードと`live.thisdeivce`

値の再読み込みと`live.thisdevice`ではどちらが早いか。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initial-timing/03_store.png %}{% endcapture %}
{% assign caption = '値を変更して保存' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initial-timing/04_reload.png %}{% endcapture %}
{% assign caption = '再読み込み' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

こちらも`live.thisdevice`の方がロードより後に実行される。

`live.thisdevice`は *Parameter* を上書きするので、
確実に特定の値からスタートしたいという場合に有効。

それ以外の場合は *Parameter* を利用した初期化のほうが柔軟。

