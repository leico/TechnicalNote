---
layout : post
title  : "pattrが保存するデータの範囲は、Rangeで指定された範囲内のみ"
date   : 2017/07/02
lastchange : 2017-07-02 11:50:02.
tags   :
  - Max for Live
  - pattr
  - parameter
  - range
---

## 負の数と大きな数字が保存されない

負の数と大きな数字になると、再読み込み時に何故かロードが失敗する問題が発生した。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-range/01_before.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    510.3oc6UErSiCCD8ryWgkOWpRRABZ+UPnHm1osFRsqbbBEg3eG6wwgTvgFt
    vtG1C0Udlm8LyyumxqIDVk5Dzvn+gdOkPdMgPvPt.j98D1A9o007FDFaasR1
    dfsvmpiqk7C.lwFtBz2m8PHoXCFWU83UY4gfG4Z6ALftDj7pZ7no84r2fp0T
    CFrT48QaLu3gwB2wVk9.23BcaeD+wLubD7yhEJkUwk6XzGFJrY8dgbWoFVa7
    nxVUr7lEzaJVlZWScq4YKSGNhseDxP6j4h8VRhaYwLIKI7rc5iQV6E61C5PF
    CbBaIWSZzzySNlFWwhRUqljphRL3uYPL4Hkb2pKPLghy6fMk11zdMkt4PT0Z
    7hKx.+PXboTY3FgRVNvF8cqcVkBifW66hzOpXbcC9j3dNF2.CU9a5gNdcKn1
    FBGhOtJS1IQ.MtkhfpVHexMpMSBPI2EWYbFrl8JsYF3Bu1o9TnlM72OT.eI2
    dLMZ1+hV877+VN8Z0ySZzOK2HN7teGatmT7t7hh+6x+Ec4ie4+FS9jvlgGGm
    Elss97G3QkfK9454FUqdcP+zqBoYCpgMPiQHwmzQXbtcpk1hZYlagbeV6xUJ
    +KUxOg7iG6.cSOZrHVu4iJsaawBbqP52hNHlF5DA7WiQ3Zq2vXkzsZOwe51q
    Y9ip1.ZYqnWIZq7aIuS0d3y9
    -----------end_max5_patcher-----------

このような状態で保存&リロードすると

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-range/02_after.png %}{% endcapture %}
{% assign caption = '
\\( -100 \\) は \\( 0 \\) に、\\( 1800 \\) は \\( 127 \\) になってしまう。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

なぜだろう。

## 整数、実数の場合、`pattr`は限られた範囲のデータしか保存しない。

{% capture text %}
 Parameters used in Max for Live can be one of four types:

integer
: integer values in the range 0 - 255 only

floating-point
: floating point values (no range restriction)

enum
: an enumerated list of items

blob
: parameters that cannot be automated but can be stored in presets. 
Non-automatable parameters may be any type of data you can store with a [pattr](https://docs.cycling74.com/max7/maxobject/pattr) 
object: single values, lists, or strings.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

まず`pattr`が記憶できるのは4つのデータになる。その中で数値を記録できるのは整数(*integer*)と実数(*floating-point*)になる。
整数の方は \\( 0 \to 255 \\) の範囲しか利用することができない。

{% capture text %}
> # Working with Integer values outside of the 0 - 255 range
> 
> * Select an object and click the Inspector button on the Patcher toolbar to show the object's [Inspector](https://docs.cycling74.com/max7/vignettes/inspector).
> * Choose **Float** from the pull-down menu in the Type attribute's Value column.
> * Choose **Int** from the pull-down menu in the Unit Style attribute's Value column.
> 
> Although parameter value will be stored as a floating-point number, it will be displayed as an integer.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

一応実数で記録させれば\\( 0 \to 255 \\) 以外の数字も可能らしい。

その記録する範囲が *Parameter* インスペクタ内に *Range/Enum* として存在していた。
デフォルトでは最小値 \\( 0 \\) 最大値 \\( 127 \\) と先ほどの結果と符号するので、この数字を増やしたところ、記録されるようになった。



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/pattr-range/03_range.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    535.3oc6VEraiCBD8L9q.w4zHaRacT+UpprvIjD5ZCQXrahh5+dgAiqyt3Md
    Orc2C8PHhYdvLyi2LIWRPjR0IdCA+D9YLBcIAg.SNCn98HRM6zlJVC.irqRI
    aqIK7t5XZIqlCdrlK45mydI3TrErqJe8tLZv3Ql1d.CWWvkrxJ3no89r2fp0
    TwMPnn8VaLm8vHg6XmRWyLNSO1aweLy4ibesXghIkL4dB9kg.a1bPH2Wn4aL
    dTYqxW9vB7C4KSsqotUZ1xzgiXyGgLjNYNaumj3VVLSxRxeyV8wHqCh8G35f
    GC+DjRtjznwW6bLMthDkpVMIUEkXfOyfXn.krd0MHlPvYc7sE1zzdMEt5PT1
    Z7hKz.+fHLoTYXFgRVLvF8YqsVkBifU4yhzOiXbcC7j3dNFm.CQ92jCcrpVt
    ZWvbv93nLYlDAz3TJBpJg7GtRsYR.J493JiqfUa0VvqMMm5jrwPHjND24frN
    JjlCJsYFAKHYR8t.ge3q+vtfaMxHlPO6+w4ET5+pwEUp2lbZwU9Fwgq+ZlU3
    IE+nh77uGU7ENpX7K+e6IESFqYLn.HDhs194+pAHmb1utonQ0p2DDg8RYb1f
    jZKuwHjftXDF2HCrk6i12M2.49A1aGI5uDIeExNdriqa5QCAw1f+pR61lu.1
    Jj9sPaHQy6DA72CVXZaClw1Wzp8D+oGum3OpZKWKaE8xYajeO4C.jNEXSC
    -----------end_max5_patcher-----------

保存可能範囲はとりあえず、\\( 0 \to 1 \\) あれば問題はないのだが、

`live.gain~`
: \\( -70 \to 6 \\)

人間の可聴範囲
: \\( 20 \to 20000 \\)

などがあるので、\\(-32728 \to 32727 \\) とした。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


