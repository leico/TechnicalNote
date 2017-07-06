---
layout : post
title  : "bindされていないpattrを、引数で名前空間を定義しているbpatcher内で使う際にハマったこと"
date   : 2017/07/06
lastchange : 2017-07-06 16:19:11.
tags   :
  - Max for Live
  - bpatcher
  - namespace
  - args
  - argument
  - pattr
  - bind
  - blob
---

## 引数で名前空間を定義している`bpatcher`内の、bindされていない`pattr`の値が保存されない

どういうことかというと、

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/01_nobind.png %}{% endcapture %}
{% assign caption = '
途中のパラメータだけ保存したいが、別にUIはいらない時、bindされていない`pattr`が発生する。

画像の様に変更を加えて保存しても
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/02_reset.png %}{% endcapture %}
{% assign caption = '

再読み込み時、値が全てリセットされてしまう。

親パッチ

    ----------begin_max5_patcher----------
    287.3ocYQsraCCBD7L7UXw4zH61jzn9qTEUgwarIACVKX2TEk+8BKtsNMWVz
    Ny9fYmqbln1cA7hh2JdufwtxYLBJAvlyYhd4EkQ5oxD0CxfpCPwpL4jDsxdf
    3BfO7KtF9bR600ZiN7UhtZlwc7nGB4cVttbUJTbXlz3TmglFT15UnyXRkUNy
    oLZ04PG5Fa6VhCVYsA5drgLwziD0s8tF3NDG1DE0BD6Xu1ZffeIHIdss8CDT
    yRnhzv1T34caROUa2tPQ5F513pO8zdwey1MF9+vu6NtNd0ia6mNjX6rMUUbH
    AciySAZMB4vvDfdsyRNWx2ht1IGInWWQoZaN8EJEgj4jqeCgHwnxBQYMh4ew
    kcaD4Vi2JzNpISjm17M92PibJNK
    -----------end_max5_patcher-----------

`bpatcher`内のサブパッチ

    ----------begin_max5_patcher----------
    538.3oc2VssiaBCD8Y3q.49JcE1g.I8WopBYBd25UDajwjlUq1+8ZO1jKUTJ
    gktopODG4YFOGNGOdreML.UJOxZQQeI5qQAAuFFD.lrFB7yCP6oG2USagvPB
    1OjkOihctzriZvbihKzQZVqt2knaOWTyzvxvdiMT8tuyEOUnX6zNXwIOjDGg
    I41+VCiDxCIQeyuDdE.fAzOiStH4xNce1S7Va0uTyfnQVCuEFZGheuLi0vDU
    QlXuYpQ.tkuAF2NF0HjgoVe1clzuzvboFgNkk+trlKl2FpmziteRv2IRW1o0
    xamXaA131GS6GGjXI2HwJohmtujaCYhbCu8NxsqqVOPUB5dW19Dt3hSnmqio
    ZsJ5Zm23w2TnRdCdzNS4CKJq9sUxw9eCILdKJS2Toxuhyv1ROvpJLnZ9PKrz
    ia1wcMwCNIaVBoLZiloJXBZoK0IfOPyWVk+bWhAT941BIcBsPvY+uK7OVKMT
    adJHbbdcxnB35gEPxobO.a79dTp1SA.yFStWxF.yTMHSVNV8Ogb.tQ0bwu93
    LfbV6WqQsxN0tdP5eOwEktUlJZtfp4lKFNGj88T1fFbeXxHg+vPJYJHku.HY
    ui6OiT1RfT9DPpey78gT1TPBuDHs9CS8VMuJB2wKZSyAlp0GM.hoYyyRkcZd
    LLkKbSg6SLWLbf2GeJXgpL8ZzlFMct6KPGyRQtkJqXJQGGZMEZQ9sveBXrEE
    qA
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

なぜこんなことが起こるのかは不明。
しかし解決方法は発見した。

## *Parameter* の *Type* を *blob* にすると解決する

本当に、なぜそうなるのかがよくわからないのだが、解決する。ちゃんと保存される。

検証用のパッチは以下。



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/03_verify.png %}{% endcapture %}
{% assign caption = '

親パッチ

    ----------begin_max5_patcher----------
    338.3oc4Q1jaBCCDEdsyoHxqSqhCDJpWEDpxwYHwfiMx1IkJD285eRaCEgDH
    0thMNZduwdxa9NlfvUpCfAm9Z5pTD5XBBEj7BnwZDtidfInlPa3p8TKqEz3r
    n4.UKocPvyBF6ph0eawg2G3FdEWvse36fL5n1rw.13XyeNOyejtdzTnX6f5Z
    MswvzJgv2V9nGSvY6rsZUeS6TcPRqDP6kWHZLboQUSmpFNSQoqc4ZhhruiKE
    f0LULjetr4MMvFiP4hBeHJ8GEKl6+PJKmDIdcX+np19Dgf+40U81e+7msLm8
    ra26F3WWgpaFg0rz0doSII9ir+J3Qd7fWwxxaGd42A7JtJ7J9Wf2CG4H42L3
    VdGb6pXibF1BiAS2ue.zFtRFHmmaNpsUEBzKYgRtLVNKTpAObh8OOnP0tjYc
    wpWG+KNrXNNdU2tRK64AHl3m7ojOAz2ceIB
    -----------end_max5_patcher-----------

左側、 *Type* が *Float* のままの`bpatcher`

    ----------begin_max5_patcher----------
    538.3oc2VssiaBCD8Y3q.49JcE1g.I8WopBYBd25UDajwjlUq1+8ZO1jKUTJ
    gktopODG4YFOGNGOdreML.UJOxZQQeI5qQAAuFFD.lrFB7yCP6oG2USagvPB
    1OjkOihctzriZvbihKzQZVqt2knaOWTyzvxvdiMT8tuyEOUnX6zNXwIOjDGg
    I41+VCiDxCIQeyuDdE.fAzOiStH4xNce1S7Va0uTyfnQVCuEFZGheuLi0vDU
    QlXuYpQ.tkuAF2NF0HjgoVe1clzuzvboFgNkk+trlKl2FpmziteRv2IRW1o0
    xamXaA131GS6GGjXI2HwJohmtujaCYhbCu8NxsqqVOPUB5dW19Dt3hSnmqio
    ZsJ5Zm23w2TnRdCdzNS4CKJq9sUxw9eCILdKJS2Toxuhyv1ROvpJLnZ9PKrz
    ia1wcMwCNIaVBoLZiloJXBZoK0IfOPyWVk+bWhAT941BIcBsPvY+uK7OVKMT
    adJHbbdcxnB35gEPxobO.a79dTp1SA.yFStWxF.yTMHSVNV8Ogb.tQ0bwu93
    LfbV6WqQsxN0tdP5eOwEktUlJZtfp4lKFNGj88T1fFbeXxHg+vPJYJHku.HY
    ui6OiT1RfT9DPpey78gT1TPBuDHs9CS8VMuJB2wKZSyAlp0GM.hoYyyRkcZd
    LLkKbSg6SLWLbf2GeJXgpL8ZzlFMct6KPGyRQtkJqXJQGGZMEZQ9sveBXrEE
    qA
    -----------end_max5_patcher-----------


中央、bind されている`pattr`が入っている`bpatcher`

    ----------begin_max5_patcher----------
    574.3oc6VssaiBCD8Y3qvx6qrUXHA1c+UppPlfSpq.ajwvlpp9uu9BlP1RIj
    T5p9v9.F4iGOGlYNiMu36Ay4GIMPvu.2C77dw2yy.oA75m6AqvG2UhaLlAYj
    eyyeBFXWRRNJMv0BJSBjjFYjaMVaEkURjl8g5AqwxcORYGxDjcRKuo2EF.PQ
    I5WIw5wnn6BAOzuCZgg.EoeGENx27Voy4g8nMxmKIFqgZfW880CAezHiTSXE
    .ksWajgPlXK0N9y4BsnnoCMm2sPxmqIVWCgCd4yMporapdlNZ7ciYzWhXtCK
    X3Jq29FJaTc9T1.KkBv4Kdkh.i79GnYk2oSmOhe27QP+yT4kdDgpkjK52Q3H
    havcjhLEupuzLc7Qyak1yB7FxadPLiwkXIkyxFxR89VGtBEljHxHLbtkYjYM
    UwXLMC9eFl5vksD9dGrCeLKkb1goqUmYVyibgbA14Rkg1kLpH2qUURcpIZBI
    0M1gkb4FLTx+ETWofZnXbAA0z1shBp8pOs1poDTJ3bh3lzLaLm+rMbVQy1oE
    MQC99skH2Ev64hJrgvj4jX4X1g05r7KkmtG8vMdh8hRVweIRVlkgkT1e+ubl
    nSiedFrg2J14Hw86GfSQXgpGmxLMoiLR+6WZilrJsXlP+qXRecJ.cIlhWClR
    VBSaWCl1tfrmKE+wXJdILE8FlrRQbccGQzzasgDUa6SbgdZZfYJkYmZtMRcs
    RG0Y+FCBVnZLkptxV6sMviIaf1sxKHBVKs+1AEyu5+G.gul+jC
    -----------end_max5_patcher-----------

右側、*Type* が *blob* の`bpatcher`

    ----------begin_max5_patcher----------
    612.3oc6VEriaCBD8ryWgE8p6JichcR+UpprHIjrrxArv3zrZ09uWX.bb555
    0Iq6ldnGBQLLLO+l4w.uLK.sVbhViB+V32CCBdYVP.XxXHvMO.cfbZSIoFbC
    wo+Tr9ITjcIE8jBLWIYbUnhVqR8qwaNv3kTErOryXEQs4QFeegjtQYwEG+Pb
    THNI27WVpYLI4g3ve31BaKffF0uhi6DbQixG8Xm0Z0ykTvajwvqylYFh9nTi
    VQ4aC09d0TKA3V9RXb0PTKIoep4it0j54JpMzHTaT96xZF+1JnNRmOHow2IR
    utQoDWOwVArwVGm6G6kXwWIwVS36uujaYxH4Fd0cjaWpVORjbxAaz9BtnyIz
    y5XhRICubwq736bPIuDOXmo79SJo+Qkbj6WeIFmEotcpP51wYXqIGoaKznp+
    PKLzioq31t3Asos.DgyEJhhI3EsIIWjMrUpsonxBJmr1hKFVSWK5BSa7G.oi
    jxFpXm2r2dWTX7irZlGnn25Pofuu+Z4EtU+nPpFge9LcpcIPk4+aRkbmaO1i
    j6V6cNeD8NwY+WwMsJt1p06n352uITwsS+o0b31jNPC7EwCpbVzuxIoM1usR
    4eg0Ng7.A.LaHc1T1x+FyFIiNcj9OQ5.VFUx3+96wAxYreYNpVzH23Aw+BxN
    mY2pOJy3vowNNYdAswodqCiFI7mFRwiAo7I.IyqZdejxlBjxGAR9h4GCorwf
    DdJPZwmV1K81TD1iWjppiTYsya.DcylmDRyz7HXJiamBWjpuQzbMh0+4fEhT
    2qQoazzXunDcJaNxtUwVpj2vbWsoQ90Y+BfS5XYJ
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/04_reload.png %}{% endcapture %}
{% assign caption = '

値を変更して保存、再読み込みした際の Maxウィンドウの出力。
*Float* のままのパッチだけ、数値が0になってしまっている。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/05_bang.png %}{% endcapture %}
{% assign caption = '

*bang* を送ったりして中身を確認したが、やっぱり0のまま。

しかし、 *blob* になっているとちゃんと保存されている。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr-nobind/06_reload_patch.png %}{% endcapture %}
{% assign caption = '
再読み込みした直後のパッチ。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

