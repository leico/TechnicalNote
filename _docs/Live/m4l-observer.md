---
layout : post
title  : "Max for Live: live.observer"
date   : 2017/03/29
lastchange : 2017-06-07 21:44:05.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - live.observer
---

## イベントリスナーを使う

> プログラムに対し何かの指示を行うようなときに用いられるのが「イベント」というものです。
> AWTには、さまざまな操作ごとに異なるイベントが用意されています。
> 例えば「ボタンをクリックしたらこのイベント」
> 「マウスを動かしたらこのイベント」
> というような具合ですね。
> そして、こうしたイベントが発生したときに
> 何らかの処理を行わせるためのものとして「イベントリスナー」というものが用意されています。
> 
> イベントリスナーの中には、
> 発生するイベントの種類に応じて呼び出されるメソッドが用意されています。
> 例えばマウスを操作したときのためのイベントリスナーには
> 「マウスボタンを押し下げる」
> 「マウスボタンを離す」
> 「マウスポインタがコンポーネントの領域に入ってくる」
> 「マウスポインタがコンポーネントの外に出て行く」
> というように、たくさんのメソッドが用意されています。
> もちろん、イベントの内容によって用意されているメソッドは違ってきます。
>
> ---
> * [イベント処理の仕組みを理解する \[Javaプログラミング\] All About](https://allabout.co.jp/gm/gc/80617/)

別にJavaをこれからやりますというわけではなく、
このように変化があったら通達してくれるオブジェクトがあったら便利ですね。

そういうものが無いと、`metro`を使って一定時間毎に`live.object`へ *get* を送り続ける処理をしなければならなくなります。
これは処理が煩雑になる上に、最低でも`metro`の指定時間分の遅延が発生します。よろしくないですね。

`live.observer`はこのイベントリスナーの役割をします。

参考

* [イベント処理の仕組みを理解する \[Javaプログラミング\] All About](https://allabout.co.jp/gm/gc/80617/)
* [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)
* [live.observer Reference](https://docs.cycling74.com/max7/maxobject/live.observer)
* [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)
* [Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)

## `live.observer`


> ### Live.Observer
> 
> In some cases, we may not wish to alter the values of a parameter,
> it might only be necessary to listen to them so we can then do something with their values. 
> 
> ---
> 
> It would be nice if Live controlled the slider in Max for Live and vice versa,
> to give us some visual feedback of what's happening.
> 
> For these cases, we would use live.observer which actively monitors any change in values of Live objects.
> 
> ---
> 
> After we have configured live.observer with the object ID from live.path
> (so that it is focused on a parameter) we can send it the message **property value**
> to tell it to output the value of the parameter it is focused on.
> 
> ---
> 
> * [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)


> live.observer is used to listen to changes in the values of properties of Live objects.
> This object works in conjunction with the live.path object,
> which sends id *nn* messages into the right inlet of live.observer.
> 
> After an object id and property is specified,
> its value is sent out the left outlet. From this moment on, 
> the value is sent on each change of the property ('notification')
> as well as in response to bang messages.
> 
> ---
> 
> * [live.observer Reference](https://docs.cycling74.com/max7/maxobject/live.observer)


Max から Live を遠隔操作できるけど、Live の変更を Max が追従しない。よくMIDIコンとDAWで起こるやつですね。
`live.observer`でLive 側の変更を受け取ることができれば両方とも反映されるようになります。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-observer/01_observer.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    753.3ocyWssaiBCD8YxWgEOmMx.IPx9qrpJxI313tfMx1vlpp9uu1iiaRSok
    KQjceoTFeg4blybIuNKHbm3HUEh9I5WnffWmED.lrFBN8dPXI439BhB1VHm9
    GwtmCm6VRSOpAyURZEkmipjhJpT+heC75RQstfpgSGcxJKGNj4h9QRheqJ8K
    ETvt2h6j5WpnNOLLD8v46kwu9ZqH58GX7m1Jo60tiDgSVfmiRVsw9HBmYeDG
    s.idvdl2lMy9m42F30nFRQMEUzNpiaA0wC.0vkGN+VfebL.+3USA78wdE8DQ
    z6feT18H3mlBvNJJYJPeAqgtvrf8q1abmd63NtSbmgWC3dU5Yb6+rjFZ9VmW
    ukn0R1tZsqPPv6vOHbqIWVwTZJeO3MQfcf2FJ6AzjpfkSkdj1PjbRI8qVt8j
    nJhzbFMUtkxI6bbWaL7pulOMYROVHH5QKnVtIyoqbzK9SD66L52vsPlh3QuY
    u8KQXgf+z2vPeXupCBotua1yF3VVqzD1.ZXQaqVyY520rN4vI8v3jEl.gg7G
    dHG2RHe4PRpLhfcD9Sm0.OJjkDHNmNRUQLjtYkCSZM19WkAeOpttLE5sjkMg
    EWUTYSWEFt.3qGnPXzUXS1f+Te0+YUX+NJTefoxoMr8eQu4jOShqF.IBYRFh
    jw09GiUNEm3ZViilL8j4ie.Y+us1IVzRx9eqPXTI6noDiikPMhh5x9SVICUw
    cKSyEmAEZh2z97Lv0ZPJ+5A6gazZ+i7lRTK26ctSoOnyB4bpRy3DMSvuXO1h
    sWroCr7bJ+xBy4LksVMPQ3VCf80cryJih5veV+g8LotSROnm02W1oK24JedR
    8mdDrtxm6vc74QR6vL9Rji1+r+jitU2o2M9xNnZ29S1+UwOfdhFa76F4Kbe3
    qU2u32x93O36l+zG4zXqF35rPppZrCs3tRvQL8deV.A2r4vqLt6UnUYnzzS0
    ueXnpPhzzaSaZrUKccKOl5ldOrTXjI7Z1oLcyW9sY+kZARER
    -----------end_max5_patcher-----------

`live.observer`を使ってLive側の変更を`live.slider`に反映させる。

`live.slider`への入力は、`live.observer`の後に`prepend set`等を用いて

    set 0.74
    
といった値にする必要がある。

1. `live.slider`へ入力された値が出力される
2. Liveが変更される
3. `live.observer`が変更を察知して値を出力する
4. 1.へ戻る

と無限ループが発生するため。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 受け取れるパラメータ

> Not all properties can be observed, please consult the Live Object Model to see which can.
> 
> Besides properties, it is also possible to observer children of Live objects. 
> Their values are object ids or lists of them.
> 
> ---
> 
> * [live.observer Reference](https://docs.cycling74.com/max7/maxobject/live.observer)

`live.observer`はLiveの全てを監視してくれるわけではない。
監視対象になるのは特定のプロパティと子オブジェクトだけであると書いてある。

> ##### volume
> Type
> : DeviceParameter
> 
> Access
> : get
> 
> Description
> : 
> 
> ---
>
> * [Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)

`mixer_device -> volume` に関して抜粋してみたが、ドキュメント上では`live.observer`には対応していない・・・？

ドキュメントが古い可能性がある。現状は試してみないと`live.observer`に対応しているかどうか不明である。

## 子オブジェクトを監視する

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-observer/02_child.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    716.3oc2WtsiaBCDF9ZxSgEWmtBi4T5qR0pHBXk3sDCx1IMqVsu60dLNKcST
    .RVnU8lgvDe32edFOl2V34uo9DU5i9N5GHOu2V34AtLN7Ze2yee9ohpbIzL+
    8ToLeK0eo8+TzSJvOqDEhzFbDXiAaBXSAaFXItNxOru9fphpfgE25kUBCV8l
    W9VTrqoR0qUTvuyismpWanVo66id9iwkwcCaXqylbUwNFe6ZAsPY6BNI6ofk
    n3rXyivjD3A9o.zyl979hEFyxu.pfMK7P.NgDvBHJDPTXxvABYRABIB3QT..
    hvrGhGb5uzR9BbTwNRepdijJNREWegeVgBpjxU4JVMuqJSIFgEQfGAslyK1N
    3hrZL3Z48frHLD7DYCdvgwevL2TmejVtVKGceVmqTB1lCJaBm2Yz44utgJjL
    ohxK.EgA+.y+hHuFmMTdIpQTqmK0q2Np61vmrJqO3GE73wp39AOACfOHcJBV
    UHkHu3mRT0siT6trwiXYaG8aD40O.HqHWD48WIasadW1jm2EFQ9+LuqKFSmz
    LHGBm3LnhJVyZYUsZLYQjjQrz+XFtuLoVPLCYR5IeGx7q0Rp55vfbEXDO1zo
    6BCjD6AJwg.FvqlGL3NhMX37HZV3A1d1hiG1BeSBOT6XxR5QVAcvLXLgDax4
    aMHfwUtG2KLZujLN.eUX.CqdMw+7GT.inw+eRHY8AQgSltpmH7Y0URkJFGtC
    RmFYtVWmFsiUVRg+Ov0MlLeSEEnUvU2qFkdB5QOla5zoQSodLGEzKe9jnmR8
    DO.7PhGibbAwhR8ENdbdsZH6ewyV7j4lQ8qGx7EeGLjMvUyW7cxPx+ylu8qz
    gvmr4kO8pmz4SOQCY+J4eqyihdjyivWTtKuo4n4aHrSBHMco+WpglmtDdkws
    uBUx8E5Z8t1CW90OWnK3pzUaOHrEyOkXuji+9Z8DyOvZma8L+9heCHuvAtI
    -----------end_max5_patcher-----------

子オブジェクトを監視すると変更があった場合に子オブジェクトの *id* が出力される

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

このサンプルで監視しているのは2つ。左から

1. 第一トラックのクリップスロット数
2. ライブセット全体のトラック数

トラックやシーンを追加、削除すると出力される　*id* の数が変化する。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-observer/03_min.png %}{% endcapture %}
{% assign caption = '減らせば減るし' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-observer/04_huge.png %}{% endcapture %}
{% assign caption = '増やせば増える' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

