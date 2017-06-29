---
layout : post
title  : "Max for Liveでbpatcherを使う際の注意点"
date   : 2017/06/29
lastchange : 2017-06-29 19:09:28.
tags   :
  - Max for Live
  - bpatcher
  - namespace
  - args
  - argument
---

## `---`はデバイス内で共通の値になる

参考

* [Is "---" in Max for Live, a equivalent to "#0" in Max? - Max For Live Forum \| Cycling '74](https://cycling74.com/forums/is-in-max-for-live-a-equivalent-to-0-in-max/)


タイトルの罠にはまった。`#0`の場合、`bpatcher`ごとに別のIDが振られるが、`---`はデバイスで共通のIDになる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-namespace/01_dashes.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    687.3oc6UssaaCCC8Y6uh.8bRQbVZp2daeGCEAx1xNrUVxPhNMcE4ee5huH6
    jzgh81PdwMkGRIxCoN7i3HRl7DSSV7iE+ZQTzGwQQNSVCQc+eDoldJmS0N2H
    YMTL+.SQV5AOBr2NBZHC3.9t0kjNDYYolg9yd8CqWZ+r34NPtL+UVQghVoyU
    RN2515NrbNj+JdPIaqNDZmInYb1gKCvCb7RfrpZYAahEopvj7AVb0CHp1qX4
    cYaxie2luIace2jN7mgzuQwzLARQPJBqYnvQRxrWVkrlbEm+R2hnsFDbFpmT
    r0Yrhv6z3krEm6Veax2I6ZkQjRvPTLkdZdGQnMMAliBBw1+eQ5NnmVNXBDdS
    eavjhYGC7wucvJUYHWzTysJWefbZ2Vx3wX5NJQK3REuwyw8ozHS0QTqcL0ts
    tgosoAiS1Nctj6SI6zVZ5xwu6LwF5Kq.PaC+qDSVkehMfhMS3MLAHlOKL.Wv
    JosbbeoTfZ32t5OYyCWEujlytYvBZsm79oBn7d5iTofBovlDSZkVy8WmcJyw
    cOFVLNODzlqDrYx0P62.TaJxVcFU4dvyc2vldPTJ4SgFhiyJwN3FPHlwhnr4
    1fJn5vmDalz.V+YmsCQuuU3Q2aDxv8Z5worMR4bA6MS0O63OQEPMEYH3aAaV
    O.dgbTxTjiWAov7HIm8FTfdksvgAi6PS+PDYnKW.ULMN0FZjMmZQiu6I8.Ss
    8Z06QVcC2TEScXh1e3K9vc.SrOaWPGisbDFYm7opZwpUqPahGfdMoV+S6mbO
    oc+dylfQ04ZpgmlWzCeuoaRmPlD10DOuAUcoJZROv4339eL99gChdhawywit
    49ZDvtuJcr+l9uuIM8usHM89dz66QuuG89dz66Q+eZOpyw4JpVNblR5LUzKU
    Pus54bkSyMeN9O.5bhX4
    -----------end_max5_patcher-----------

`---`の値はデバイスで共通になる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## `#0`も使える

じゃあ、`#0`は利用できないかといったら、利用できる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-namespace/02_sharpzero.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    703.3oc6U0zbaBCD8L7qvi5U2L.wwg1a82QmLdDf.qDgDizhsSyj+6UevGRX
    6zloW5AeQXuuckV81Uu8s3HTg3DQgV88U+bUTzawQQVSFCQC+OB0hOUxvJqa
    nhNLTtmHQqcfGnjiGnJZAkQgWMtjNfHpqUDvs2I2kr1rr5oAPln7ERUkD2nJ
    kBFy3Vx.VIiV9BrWJ5a16amvwELx9yCvAb3bfhlVQEIvhPVoSdOK16Ck2rSR
    JGx1zs2ax2zM10r7oOSoemjnHb.CTA2+NSqrjjn34ullftfyepSg22R4LBnB
    trsEjJ+yT6knGV51XYxUIGJkQnZplnHRUXdGgvccdli7BwT+eVX2nGWOYhxc
    ltexjjXZCbwuYxJVpIWPem6k15.5z1Mn4sQWcj7dpMUbFeOdLklYpG9lkaRr
    L01M1loM4dsSlJcof4RIS2Vd9540s5X88kTQASA+yDSQiqi0ih0c3cDNkurW
    XBthTi6YvtZAGTzeYu+oY2cQ7ZbI4pAywsNx6GRJlMRenFIsRvMIQPozXd73
    zcYOX4tG7uLVO33tKDrtyUS6WATouj8pBrz9fmYOgrQPPHXgPSwwH0v.bGky
    Wvhfn65fRZy9OH1BgFr8i1aKhZWO2gtSKjA6T3CgrMfYLN4n91uX6Og4zVLP
    .pqDjkLAdlbTZHxgKfToejTRNRq.mxleyf1cZ2XSDZpJWQaHJHzFnkMCsnfW
    cjtmo9Qs5c.osiouEgNDn86+h2eFPf8EyBFXr0yv.4jKUkq9RBXxaOvKoz5d
    Y+n6gsYMKyqScojp+t4z7fW6FZzQnfvtj14UXpyEQSGAdONd7GyOeXT9Hus5
    o3Y2rqZ8qaSRmqu4+6CRy+SyQyuMF8uaLZts4IO6Q2itgOo2lidaN5s4n2li
    9e1bTqiKUTMT3BkzEpnmqfdc0ykJm5S983eC4mAhpB
    -----------end_max5_patcher-----------

`#0`を試したら使えてしまった。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

いやいや、使えたとしても他のデバイスと被っちゃうんでしょ、きっと。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-bpatcher-namespace/03_noconflict.png %}{% endcapture %}
{% assign caption = '
・・・・・・被らない。`---`の意味は一体・・・・・・。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

で、ここまで調べた後に見つけました。

{% capture text %}

[stkr](https://cycling74.com/author/53506fba746b61d82fffaa62)
: Aug 02 2015 \| 8:12 am

---


yes in that that to get unique ids in live you can use `---` and `#0` in max.

however, no, they are not the same.

there is no equivalent to `---` in max. `---` is device wide,
throughout any abstractions / etc in the m4l project format. 
you can also use `#0` in m4l for unique ids in specific abstractions exactly as you do in max.

the sort-of equivalent of `---` in max is to use globals (no precursor), 
but this is not the same as it is program-wide. 
you cannot do this in m4l as it would send data across the entire live set.
there is no way to have top-patcher-level-only specific sending in max - 
you need to build system of passing unique ids around.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Is "---" in Max for Live, a equivalent to "#0" in Max? - Max For Live Forum \| Cycling '74](https://cycling74.com/forums/is-in-max-for-live-a-equivalent-to-0-in-max/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`#0`と`---`両方使えるけれど、`---`の方はデバイス単位で共通したIDが割り当てられる。
Maxの名前空間は全部グローバルで、親パッチやサブパッチに送る特別な方法はない。と。

