---
layout : post
title  : "Max for Live: bpatcher、pattr、Link to Scripting Name"
date   : 2017/07/01
lastchange : 2017-07-01 11:54:08.
tags   :
  - Max for Live
  - bpatcher
  - pattr
  - parameter
  - save
  - load
  - args
  - argument
---

##  外部サブパッチを読み込んだら`pattr`が上書きされる

ということが起こった。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/01_subpatch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    429.3ockSsraCCBD7L9q.w4zp3z7Ps+JUUV3DRBoNPDr1MUU8euKKfkSiSeb
    HDwrqYFlc3iBln1dV4E7m3OyYrOJXLBJ.vR6YhixyqajdpMw1Fqo8nXRrTmz
    YjGUTEDtV4xUvcZSiBnupLAdRBq2qM6pbp0Pj0xY2OcBe9ig0ESCqyPD9Kou
    Xq0cTRstLgn2PrYqOb2CCHy1BY1l0ylC0FnbUJirtgT4zTMO7dDPjOi3A.ue
    REEFVfKpklcB9KgN9rnHrL4O5SF0anDGym.kGx3f5Lc4BNC33CK8+Lv35hUW
    YfCrqxwsqG9eVB8q+38xN0lJ7zQ8TEtC55VHlnX8NCSHMFKHAs0T06CIVPIZ
    zfV1D4nW4iO+JoZ3XXH8879CJnS1zprayvY7grbCcLRKCEzHc0nMuFtl9a1f
    0rar7vEM42acvu1Ud5LMVhRo4+RQV5lHPQ88W5TZJfeYN1aacqyi7TvgW1Ox
    2fJQang4fdv2hAO6JZkmN0ob9TyDE3CkCVWX6pIzVsItkRhBmpSm6eNgHcXn
    GvDVqKZFmWNWD+T6FkyzpSQCj4OK9BPER4hF
    -----------end_max5_patcher-----------

読み込んでいるサブパッチ。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/02_overwrite.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    324.3oc4QsraCBCD7r4q.4yzHbaTCJ+JQQUFyFvIFajsgRUT92qefnNJ2Z5s
    dYgcl0d7ry0LDtVMCFb997C4Hz0LDJ.4APK8HbOclInlvX35Apk0AZbQjbhp
    kzd3NtCjiqzb3yItgWyEb6W9oHKLpSmLfMJc4lxBeI+3BoPwt.MMZZqgoUBg
    erxENlfytX6zpw1tTbPRqEP2iGHRL8HQcaupAtCQoabdKAQN1ykBvZRAC9jK
    a+PCrEKTU48.gDpuVs9Y0RCZv.RK0xUxz8.uIr6T0megTh+QU0n8AYSthTo2
    EUyW2E+ujjn7Z9XAiciKMcOerm5VVluT7GF8+6xcRLp29L4d0uK1etTOLHlN
    LLAZyxaKD6tP+rJrK1UDZ4xX6agVM3y037aCHTsaoXcuqQcTw422hiG0sl0x
    Qdv2Ydkuk8c3ZdQh
    -----------end_max5_patcher-----------

読み込み時に上書きされ、全て同じ値になってしまう。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

この原因はなぜか。



## 埋め込んだ`bpatcher`では起こらない

`bpatcher`の *embed* を有効にすると、この問題は起こらなかった。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/03_embed.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    922.3oc6V0rbaCBD9r7SgGc1sikicrSu0mCOYzfjPxjfAM.xwoYx6dW.ILHK
    4oSR5gNUGBQd+Vfc+1eXeaVTbF+LVFO+Gy2OOJ5sYQQFQZAQs+NJ9H5bNEIM
    pEmUiT4Gvh3EVvSHACcDGfsO4QGLA+xIhjjQnD0qZsRZQ3kkRrxd0K+9xE5k
    4O1BR44OiKJDnJYtfSoZ0V1hkSI4OqNH3MUG7kiYnLJ9v0avBb5Zfrpi7Bbf
    Dtn.7MOIrliDFEqj9BM9IgUkJv4stvtsZeHYsYc0N2+btTs.KwLERQ3LedfT
    X3Nd1SeKYYGsgOlgK70BLCdi5J6v6Lu1VVoW2Z+dYhmo3BXJrT8cH7B9Sruq
    YofHWJPTbIAXPrPFZ7Qwn5ZOwQdaQm27D2bPaW3DQXVQ24DIv57C69W6jhD.
    Aq.OpQXM0y2uN9xw.gMAqgXLEqv2m0YRW3g0Kev37OXBG2u1jlsdmWhlNGvl
    q4QqPtYMlQX8iXN3BbIpgpRK4Lkj7KiAZX6AvKQ43Q2rKP7SAAQ67u3JAofy
    zFQ.WqE2cc6mmrw3aa7cFiFLT8.aFxu.dYDPI3jMxLjvTpRM2vpNPEmSCgb6
    ihKUsv0DFqGKp30iCJHUGtwdy3.3wac1FDYZCyhlBcnToRzoP1VgnTF9Ev66
    c7mQLxQjBqH1PvpkNvqZjjDhbZ.jBHKNG+BoPY6I4mL.pSp6RhhcQ4BRET9E
    JSAM7BkHUuZIcOQMcMZSU3i0TvKBUHnoteIoey8.48ZxWR4PCGW8Vu17.Tl6
    AfqaRl3ALPiRaao0lJyMlJxUq7xgMMa3BHznU+dOodMJuq2k60ZbUvsK.aVA
    7jMv4mBLL2ZpTLGl5051BM.DdZCwphm+XmVuOaV2GK9fbbaV4Hbrty7kmQs4
    F3y1rEfTUh4ZM9bw.65lsCFC7eVZb19tOFaZ9K35zEtEo1lToZ+ij0nrIwQW
    QjvyCLFu8YOGk4cqf4yHJcKU63E9W0voEINb2CIWLKm87GXYmPzFLuzG1G2+
    1ugMNfZ9F6HZRIrm0zg7lJwYU2HMKPW4AtPEn7XZ1EfWdA1Ui3+oWgykms.6
    dr9UljWM9vUTRdiHuKypMWcdRPlEz+UQXtGw265fno7AMqYWjXVs03eUCG+e
    2jwI697SFu6KZv3o4hmlKdZt3o4hmlK9e34hmFJdZn3+1CEOMQ7smH1bf8mx
    QaF8lto2jMWOUy3SzzeZF3lee1uA40jKWA
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

じゃあ全てを *embed* で作ればいいのだが、いやいやコピー&ペーストめんどくさい。
サブパッチ読み込んで終わりにしたい。



## 2つの差異 *Parameters*

2つの違いを *Parameters* 上で確認できた。
どうやらサブパッチを外部から読み込む方は、名前が被ってしまっているようだ。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/04_conflict.png %}{% endcapture %}
{% assign caption = '
外部サブパッチを読み込んだ場合
: 名前が被ってしまっている
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/05_unique.png %}{% endcapture %}
{% assign caption = '
埋め込んだ場合
: 名前が同一のものは後ろに数字が追加されている
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

*Parameters* は Max のツールバー、**View** -> **Parameters** から確認できる。


## なぜ名前が被ってしまうのか

いろいろ試してみたところ、`pattr`の *Parameter* インスペクタ、 
*Link to Scripting Name* にチェックが入っていると
外部ファイルを **普通に** 読み込んでしまった場合に名前被りが生じることが分かった。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/06_unlink.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    420.3ockSssiBCBD8Y5WAgmc2X8Z18WYiogpnhoBF5ztZL9uuCCPScs6EePL
    blCbN8LCWyXhR6YUsf+N+CNicMiwHHO.KtmINJOutRVSzDaqrllihQgRsRmQ
    dTQUP3RkKUA2oMUJfNUdD7jDVuWa1U3Tqgfp4Sdc7H9r27qyG6WmfH7UwSr0
    5NJIpKhH5MjZ1xCuLsmX1FHo1jN0bn2.kqPYjkUjKGGqUCWB.hzcDt.3xIUv
    XXAtnTZ1I3q7Ltkk4WF8OyIi5SzhCkSfpFR3f5L8w4SFvw6W54Bvv57kODf8
    hq7giqoOWjP+5t9ZYqZSAd6neJ7eC5xFHLQw5RFlPZLVPBZqonKGhpfVznAs
    rJnQmyGt+kS0v1Pe46z8WbPqrpQY2lfS38U4G7w.T5anAXgOQ1MT69NR06sN
    3OYkB+wgRzPX5u3DIYTQk178GxzvhG+9wzZaiacpiFmK34cczMnSzFpW0iC9
    TyGIOHq7zoVkqNRlj.eGbv57aWNh1pMgszflvoZ0I9yHDoCmoAb.pwEBiyKl
    IBG0tQ4LM5XmGU9V1WP.XPR2
    -----------end_max5_patcher-----------

*Link to Scripting Name*のチェックを外したサブパッチ

*Link to Scripting Name* にチェックが入っている場合、
*embed* した`bpatcher`と同じ挙動になる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

・・・ということは、同じ *Scripting Name* 存在していることになる。
なんかそれも気持ち悪い気がする。

*Scripting Name* が一意なものになってくれれば 
*Link to Scripting Name* にチェックが入っていても問題なくなる。
ので、引数を与えて回避する。


## `pattr`用の引数を`bpatcher`に与えて回避する


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/07_argsubpatch.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    423.3ockSEsaBBCE84xWQS2qtEPcyr8qrXHErp0AsjRggw3+9t81VB5bK5CT
    ROma64x4d3TBgUnGDsL5GzOoDxoDBAgb.jvdBqlOTVwawxXaqzptZ1LOUO2n
    30BjAfKDlHCrSppDV7TYAvFtsbuTsK2HJsdUyl+R5L5x2cqul5VmCHz0gSrU
    ap4XouEPjaP0zEGddwDwzc1nZyGUy.8lUXxEJdQE1koAtV6QO.KdG9KvdrQ3
    aLffxJ3pcL5ZWEmSRbKytSeRI9FZwa4SOkkaEs1HkULfeeNywZnWw9X1nec0
    peYiSLsraaZKdLiAeFudCzwZSfOchts7dwlbPVnQyceexhNqOvQFMNBiqTZK
    2J0p7QaJH+sGiYHGLMlJy38+OJ0yq5D5sQ3H9TUpjpubcwTK+xBzpc+wz7h5
    Z2qM16ovn2l5ovjV7UH1gNJCZsq+aEyBN7Kyhs5NSYbfEF6zrw4xFnSjJzwm
    TC7+DM8hztWVdSSuvzFJFk.B6GzF21UyvsRkeKlif.QuLV+RDgafHqEhAc9b
    Ba3skL+Q0aDFUmLLWAkOm7iHHxAz
    -----------end_max5_patcher-----------

変更したサブパッチ。 
*Link to Scripting Name* にチェックを入れ、
`pattr #1_test`と引数を利用するように変更した。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/08_argbpatcher.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    341.3oc4RsraCCBD7L9qvhytUF2nFq9qDEUgwarIACV.1MUQ4eulG0knbKs2
    5kEsyrvr6xbICgaTmACN+s7c4HzkLDxC4.PwbDdfdlInFeY3lQpk0CZbQfbl
    pkzA3Ftcj8qzb3iYtg2vEb6mtpHQF0gCFvFjt74xBWHeejTnXmf1VMsyvzJg
    vUVYjiI3rS1dsZpqOEGjzFAze+EBDy2SzzMnZgaPT51kYKAQNMvkBvZRA8yI
    W18tFXwQndqaFHa7wp50i0QZTCFPZoVtRltG3s9cmp43SjR7Opplr2IaxSjH
    MgTEjyGKIqGqZu9CYAi84k+ykA3aon5tnAnJeuC5ZVlKT7GZH924FH0+d2P8
    CYFpSsBUOpUfbiUveYLcbbFzlXC68BKNgiJ+BZagOkKCou3S0f6yNT+FOBUu
    rorKM6jNzEmecCNb0kcuVNw8KiLmxWy9BrxGeXM
    -----------end_max5_patcher-----------

引数を与えた`bpatcher`

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-pattr/09_argparameters.png %}{% endcapture %}
{% assign caption = '

`pattr`の名前が変更されていることがわかる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

埋め込んでいれば *Link to Scripting Name* にチェックが入っていても問題ないのだが・・・。
