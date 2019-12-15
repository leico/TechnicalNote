---
layout : post
title  : "mc.poly~ でMIDI系メッセージが処理されない"
date   : 2019/12/15
lastchange : 2019-12-15 22:25:52.
tags   :
  - Max
  - Max for Live
  - mc.poly~
---

## `mc.poly~` でMIDI系メッセージが処理されない

ってことが起きた。


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-mc-problem/01_normal_poly.png %}{% endcapture %}
{% capture caption %}

通常の `poly~` は問題なく動作する。

```
----------begin_max5_patcher----------
482.3oc0V1saiBCDE9Z3ovxWyVYSHAXeUpphLgQccWvFYLoIpp8Ye8OjlzUI
rtaIQp2XIe7XclOOLi3k3HbkbGziQ+DcOJJ5k3nHmjUHZbeDtksaSCq2EFV.
OKqdBm3ORC6zN4N1uQzrLDAsfb3PwPKWz.Z2EWbTTNnOnRGU8R58cfOWvXzC
iG0wza9EW73ZErQ6OMkjeGIAkUZWyKrqoo2Qd+J7ZWNYxyejlhsZuFGaWR9h
PpfNPTiZ40bXKHzmkT57SZwR6J0u4RnRKuRnJjZ3pSJkjdJozIIs3JP5sgxk
GgLubRFymSFMcmqtXqY5rVFoGaMWMcUL6+hvg1JPMYgpioXsfFTqAAqpwk2j
yxX5EYLAgqXhGCo4j59vcIYRVoeyYcrtFBq4e2QcYnjtXNaQkM6eC0OT4xHD
8SLJh7OYIabp5zeidJMt.vMbwe+2ANSr5eDwd4fZygm4w4WIni9TC8Ztfo4R
woA4i4ruhAaTwsxnxajQA8xkYBh9EMZQHFkOCDY9Kr.bpbNJRYg3Twb3DM.m
bf+wxjuyh00sET8iQ6LwLl3IoxtsHws07iWp265wJXK+P7dElxztqM85CJWh
g2sJC6uprFThAtK1XKdFKcifDlQn8cLOItIUwuF+GjAoFiA
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-mc-problem/02_mc_poly.png %}{% endcapture %}
{% capture caption %}

`mc.poly~` では動作しない。

```
----------begin_max5_patcher----------
482.3oc0V1saiBCDE9Z3ovxWyVYSHAXeUpphLgQccWvFYLoIpp8Ye8OjlzUI
rtaIQp2XIe7XclOOLi3k3HbkbGziQ+DcOJJ5k3nHmjUHZbeDtksaSCq2EFV.
OKqdBm3ORC6zN4N1uQzrLDAsfb3PwPKWz.Z2EWbTTNnOnRGU8R58cfOWvXzC
iG0wza9EW73ZErQ6OMkjeGIAkUZWyKrqoo2Qd+J7ZWNYxyejlhsZuFGaWR9h
PpfNPTiZ40bXKHzmkT57SZwR6J0u4RnRKuRnJjZ3pSJkjdJozIIs3JP5sgxk
GgLubRFymSFMcmqtXqY5rVFoGaMWMcUL6+hvg1JPMYgpioXsfFTqAAqpwk2j
yxX5EYLAgqXhGCo4j59vcIYRVoeyYcrtFBq4e2QcYnjtXNaQkM6eC0OT4xHD
8SLJh7OYIabp5zeidJMt.vMbwe+2ANSr5eDwd4fZygm4w4WIni9TC8Ztfo4R
woA4i4ruhAaTwsxnxajQA8xkYBh9EMZQHFkOCDY9Kr.bpbNJRYg3Twb3DM.m
bf+wxjuyh00sET8iQ6LwLl3IoxtsHws07iWp265wJXK+P7dElxztqM85CJWh
g2sJC6uprFThAtK1XKdFKcifDlQn8cLOItIUwuF+GjAoFiA
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-mc-problem/03_subpatch.png %}{% endcapture %}
{% capture caption %}

読み込んでいるサブパッチはとても簡素

```
----------begin_max5_patcher----------
307.3ocoQssZCCCC8Y6uBidNazz1UJ8WYLBtIhMWZrC1JYYT5+9rkSZ65fsA
6EA5nitbN5jT.6ciX.T6TOqDhSRgfgR.hobAzpGqOpCLMvhu61e.JxkHbjXX
iUUNCZ6aM1iHwMTdEz0S2ilgnO5v7M.f5koRcZp9Mi80JOVS4pkKebQgpbUJ
tbKGiHW5vzvmR77d3o4aInGvlpHTbHUZh7l88TVwhKJT.oJU0t1Vzl0CvENK
kywyxh+mC04MwY+2snE+lOrYcJtZ4O4CkvMBfI.GM16+37JR3eUUAWuud9wL
4ppqqoACjwpIiydCmxLmusVcW2.5CSj4UDssCNeJcaAmZr4T1P.ONXl4mQz9
nSPQan2ymELtYMja00fdaug4JSOq3J4WhU2hgNcVG7mSdV9I.X3rsB
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


これを解決する。

## サブパッチに `out~` を追加すると動作した

1. `mc.` だからオーディオ処理 ON にしなきゃ動作しない？
2. それでも動作しない
3. そういえばオーディオ処理のオブジェクトがサブパッチに 1 つもないので入れる
4. 念のためパッチをリロードする
5. オーディオ処理 ON 、実行、動いた
6. オーディオ処理切ってもいけるんじゃね
7. いけたし

なんだそれ。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-mc-problem/04_out.png %}{% endcapture %}
{% capture caption %}

サブパッチに `out~ 1` を追加した。

```
----------begin_max5_patcher----------
329.3ocyRErSCCCC8bxWQjOWPsaioI9UPnpz1HHUqIUItihlJe6j5zrM.wjP
vAtjH+7K4Y+rOxYPkcT4Aw8hGDL1QNiQPy.rkXFzIGq2K8DMvndwV0BYwTnZ
DIX6.9lnHAaF5zl8JjdRwYv.qDZ9BZuDqeVadpzopwXgTr517LQQ9l4qMTvp
vo3wkmnahRV0dypjhd4AUSY.J7KkRDc5pAL1YrScBClyTVa65TlXcCThINOc
Nwy9cNg17i7gDZDBesWEMA.N0ueuEsl7lcWygt6+mC06zg+9ObUYKsor9paJ
EvEM.Q.1qMed2mjXF+ick2N3pSClEWUbVlFkG0FIpslK3TD47EYk88GTN+BY
Rhfs0Zcyg6xnPsIFRFB3TGzI9QDoK3DXvFFbTYAia2.wmZaTNyfl3xmGVAIo
QhQ1o78xXePSN9D+cr0xw2D
-----------end_max5_patcher-----------
```
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-mc-problem/05_fix.png %}{% endcapture %}
{% capture caption %}

メインパッチを開き直したら動いた。

```

----------begin_max5_patcher----------
501.3oc0V1saiBCDE9Z3ovxWyFgIj.YeUVUUYfQotKXaYLYSUU2m80+.MoqR
PVsjH0abjOdbNyG1yH+ZbDtRbD5wneh9EJJ503nHmjUHZbdDtidrtk16BCyg
+HpdFm3WRCG0NYI82HRdNJEsNcZQ9PGi2BZ2FWeRTLnmTIipdI8KRvmKXL5g
wkjTc8SL99GUPs1uZVZwpzDT9N6XQocLKaU56ag03xISd9irLrU6s3X6PxWD
REHAdCpi0vfC.WeQRIKOokariD+jqgJY2MBUtPC2bRIoYmSJYVRKuAjdenby
IHK1MKiEKIilpysWszLaQOFImJM2N+oX9mhvgtJPM6AkjpncfFTOBbZUqKuS
uHiYWkwDDthx2GRwIwcwcS5rrR9ly534ZHrV7cG0MgR55ErDsqdkTz9xeQ8C
UtjBQVhtQcCsZV8STNGZ6Y64z1PXO2+CY9NTmSuK.bKi++ulvYhU+ieR5ECp
5ojzeqIAcxlFnWy3TMSvOKFSGCSVgd3he0C0n0gXzX17kLx7zi.bZ2B3zzex
7NsDFUduLp3dYTdHFUtDNQBvI2clOdC2WYQkxCfpeLZmIl1JOKT1okItolGp
oduY.VAGXSw6UnJS4t1TqOnbIF931breqhFPwGXtXis3Yrz0xhaZ41KodRbc
1heK9evB7VsU
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


