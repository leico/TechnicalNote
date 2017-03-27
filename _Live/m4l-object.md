---
layout : post
title  : "Max for Live: live.object"
date   : 2017/03/21
lastchange : 2017-03-28 01:51:04.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - live.object
---

## Live のパラメータの取得や設定を行う

Max for Live から Live を操作するオブジェクトがいくつか用意されているが、
その中から最も一般的な`live.object`を用いてみる。

参考

* [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)
* [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)
* [Max 7 - live.object Reference](https://docs.cycling74.com/max7/maxobject/live.object)
* [Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)

> ### Live.Object
> 
> Once provided with a valid ID (as above),
> live.object can be used to control the accessed parameter 
> (in this case, track 1’s volume). 
> We can test this by hooking up a simple slider object to it. 
> 
> The slider, in conjuction with a `set value` message,
> tells Max what value to send to the parameter:
> 
> ---
> 
> * [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)

IDを与えると、それに対応したパラメータにアクセスして制御することができるようになるって言っている。
実際に *Track 1* のボリュームを操作するパッチを作ってみる

{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-object/01_object_first.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    658.3ocwVssbaBCD8Y7WgFd10C2LIt+Jc53QFVaqTPhQRPclL4euRqPN1IXB
    t1M8APncWPm8r23kYAgaDG.UH46jePBBdYVP.JxJHneePXM8PQEUglExgeK1
    7T3bmJMbPihqXcvB8dlpD5XEfWOqD0Zdiuk7fWHusVzpq.M9ES6kpzOWAn0d
    6bFoetAb.LbCkuKbNIjw09ExO6MtgpK1y36VKgBsy97jUKhlSRVgKwQw3Ny8
    iujAILtGHwVYuNal817IxFaqDluwPdaZ3QfIo0fFjqANciyEiFjIR5ktUHqo
    nOjOctwRHH+LFirLGohrjL6xxn6OgbgziFIz.7RhBzjNZU6PIHw4CmfDeEjv
    noCYwm37wwo2euGqBTUrRP5wXGUxMw+Ko9T+e4XoLwilxLX5fI4jpmR9PeER
    Jde0xKxH9.AsCJWS0ZIaSqFd6IUO0zyMVm2DpEa8h8xO0CMEP6FgfNyV0dgT
    OUi8jQz.5pMQMziVLj1VNSeLYK1oGSD7K2mZBD+FE1nw.YCYeMUCO7HVMjkM
    d0PxYwdGpGIvutAjJlRC7hSXw+J1qFTJ5N3CzGqjjLDuk9uk2dLJ6JZglbmS
    WL.ZOw9zZamTsjV7KEIhTyNXxacidIchp15gZvldyCfscUvqIL1MMM20a4J5
    yhVX7T96+mD7XrxOm2ThVYgGb9exf71AUBJMiS0LA+DiROyl8rxRfe5T4Rlx
    10EItnACfSEN1QZeJbrU5eU3Y4DvSxUwO9TEosM7EGadWA36X0qDf2FCZ4l3
    oDPi++PfSBeo2B9h9P8Jsooy1t2cHHzLczdRfl+vbbKi61hMaBklNUd6wIKg
    TooMh1zCoU55AcH2M.LrVXNXdKqmaLm7qy9CaSb9JB
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

初めて登場するものがいくつかあるので解説

`live.thisdevice`
: 一番左のアウトレットはデバイスが読み込まれた時に *bang* が出力される。
ロックした状態で`live.thisdevice`をダブルクリックしても *bang* が出力されるので、
デバッグ時に挙動のシミュレーションができる。

`live.path live_set tracks 0 mixer_device volume`
: `live.path`オブジェクトに初期値を与えたもの。初期値を与える場合、先頭の *path* は必要ない。
左、中央共対象となるオブジェクトに割り振られた *id* を、*bang* や *getid* メッセージ等を受け取った際に出力するが、
中央のアウトレットは、オブジェクトが移動し *path* が変更された場合にも *id* を出力する。

`live.slider`
: 一番左のオブジェクト、ドラッグすることでスライダーを操作することができるUIオブジェクト。
インスペクタの設定より、スライダー位置に割り当てられた実数( *float* ) *0.0 - 1.0* 間の値を出力する。

`live.object`
: 受け取った *id* に割り当てられたLiveオブジェクトへの橋渡しを行うオブジェクトで今回の主題。
今回は 右側のインレットで *id* を受け取り、左側のインレットで`set value`メッセージを受け取っている。

`prepend set value`
: スライダーの値の前に`set value`というシンボルを付与している。
このオブジェクトを通過すると`0.5`, `0.37`といった値がそれぞれ
`set value 0.5`、`set value 0.37`、というメッセージに変化する。

{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-object/02_conjuction.png %}{% endcapture %}
{% assign caption = 'このパッチの`live.slider`を操作すると、 *Track 1* のボリュームも連動して動くのが確認できる。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## `live.object`から送受信するメッセージ

{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-object/03_messages.png %}{% endcapture %}
{% assign caption = '

    ----------begin_max5_patcher----------
    1135.3oc2ZssjiaBD8Y6uBJ8riKgPWyS4+XqTtjkviYiDRkDxwa1Z+2CfD1R
    dviPw1LY24Ai4hgtOc2G5lZ995UN6qNiac.+N3KfUq995UqjCIFX0P+UNkom
    yJRakKygh+6p8e0YS+TL7Ylb35FbMllCxRKJTSRxkSwW9u4iTCR6Jq5XEXlb
    6fCi1x9VAVtZ055WD6a03doywA7mCSUmxxNRnusqAmw5mMwaq6FfuernIFI9
    zCt08xOgepD5kCUL1OVuV7wFCU6BxI7Vo1NrimRanokX8SNVy8btH1M7e.C2
    rCSS22qtJ8urJW12cneZGqpLkQpnS.kofm2sFgCjFry61ga1i2CraLBbQA9R
    vEJQ5vQf6gJJqk7Ox8C4t0UKlqLzomv46RYrFx9NF952ZG.+AzW.vEc3pCpg
    UiOFGKpnucWivjU1drpgY1RUHiml4J49DRsQoj2XV6Jmho8qQ5poZVne2cB2
    jZ.eBgM589bnDaDsE3lHZfdAeb3l2DSeuP+A18c03lVRKCSyvWhVe9fGWsNB
    DeaWKlAXMoY+UKvEjUPp20VTw3czgrw5QVzBP1MFDxE2Gx4JC47BfOeBs6xi
    SnCvAD7G0U0c0.nNfHROP3dWfPiVFACkNR9xFHJxZp4LwOgu13Gkd2G.44A+
    IK.RcY+aXsnWfUPugK6irmSCCTRNy44ywmHY3wTEbffPOTAzk5CxWOb3aNbL
    9XETGWOYiHST.larQtaPqvzpCoP1gbc.OFXWgQnOE10cmpJ5JweHIq2SfjEE
    hFSxFkXQs8CoIfuVZB+33OOZBJfuRghKc9UzCh1b8jDt5AivkPRPEd9hLT2L
    JPXigvz.4fahcIGXGIsJhs2gJdQOLgv9T5aBLfG2oZlOuKuAlAWn0iUDTjxJ
    ez.GvWbZICLECQLP3qpL51BRNt4tEROc5w5efIkRquFYs2TbnnJkYBjfPRHA
    ICNRBtKhXmhbm.PyUl68WrBLbWbgtcTB6hyF7SpHWnuUhFFRRGg7+EpJ2I4w
    1mGhtKkrS5Xn9xaUE6lr.ZG4J3ZJ812vTdLhwmhasUcMYJgS8tjfqGTNtkeS
    8k2c6KWeNkQK5HIOGSGmGVNoUvBIQNWsVPikGdFe.3LxyMB8qTdDu2wrxiDe
    fVQdDYDLu8JdI3ix2sQPSJBjeL.iyJA7lS.CmQ.KI40U7bUF7piCc2BC4+Ew
    ucNNZa.OeVu.Qir20S64Y2EpAxD0vN1co7LqcOvdwEFwa3aO4w2j3TKhOglf
    OQ1SdBLQdBsl7XFOF5Q3wdLADZT.mm8Lf7BjA9yHOP+kwqF5mzSh1mhWj2kd
    uBJUWCnTWrF.gVVC7dxZPPDxxZPfIZfktVyDVaW6FjMaPOztxC7+QxCz.7Yo
    A.9IQJW9fIA.uh75L6dmG4ZG3icsS3y.ged3Ezj7Ff+Wyanu70z55ShGCneO
    khBu.+uVIgynMxtDZeWYs2NM7B2UqW9tCNoM7ppY7Rp6Z5KI+bX+yiH+OKog
    1QFrM7S9Gq+W.DmTTjA
    -----------end_max5_patcher-----------

参考パッチ' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

### *id* について

> live.path is sending object ids out of its leftmost outlet 
> connected to the rightmost inlet of `live.object`,`live.observer` and `live.remote~`.
> This causes these objects to operate on the object selected by live.path. 
> 
> ---
> 
> * [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)

画像や引用の通り、 *id* は`live.object`の右のインレットに送る必要がある。
その他のメッセージは左のインレットに入れる。


参考パッチには`live.object`が3つある。
これらに割り当てられたパラメータ、及びメッセージについて個別にみてゆく。

### UIで操作できるようなオブジェクトの場合

参考パッチの中央は、上記ボリュームを操作するパッチに手を加えたもの。
`set value`以外のメッセージの出力結果はこの通り

#### `getid`

    track1_volume: id 2

当たり前だが、 `live.object` に割り当てられている *id* の値が出力される。

#### `getinfo`

    track1_volume: info id 2
    track1_volume: info type DeviceParameter
    track1_volume: info description This class represents a (automatable) parameter within a MIDI or Audio DSP-Device.
    track1_volume: info child canonical_parent MixerDevice
    track1_volume: info property automation_state int
    track1_volume: info property is_enabled bool
    track1_volume: info property is_quantized bool
    track1_volume: info property max float
    track1_volume: info property min float
    track1_volume: info property name unicode
    track1_volume: info property original_name unicode
    track1_volume: info property state int
    track1_volume: info property value float
    track1_volume: info function re_enable_automation
    track1_volume: info function str_for_value
    track1_volume: info function __str__
    track1_volume: info done

割り当てられているパラメータのプロパティや子オブジェクト、関数、*id*、説明といった情報が出力される。
ここに挙げられたものについて`get`/`set`/`call`という操作で取得、設定、呼び出しを行うことができる。
全ての処理ができるわけではなく、プロパティ、子オブジェクトによって実行できる操作が異なる。

#### `getpath`

    track1_volume: path live_set tracks 0 mixer_device volume

`live.object`に割り当てられているパラメータへのパスが出力される。


#### `get max`

    track1_volume: max 1.

取得可能な場合、パラメータの最大値を取得することができる。

#### `get min`

    track1_volume: min 0.
 
取得可能な場合、パラメータの最小値を取得することができる。

### 少し特殊なオブジェクトの場合

右のパッチは *Track 1* 自身を操作するもの。
このパッチには以下のメッセージを送っている。

#### `getinfo`

    track1: info id 3
    track1: info type Track
    track1: info description This class represents a track in Live. It can be either an Audio track\, a MIDI Track\, a Return Track or the Master track. The Master Track and at least one Audio or MIDI track will be always present. Return Tracks are optional.
    track1: info children clip_slots ClipSlot
    track1: info children devices Device
    track1: info child canonical_parent Song
    track1: info child mixer_device MixerDevice
    track1: info child view View
    track1: info property available_input_routing_types dict
    track1: info property available_output_routing_types dict
    track1: info property input_routing_type dict
    track1: info property output_routing_type dict
    track1: info property available_input_routing_channels dict
    track1: info property available_output_routing_channels dict
    track1: info property input_routing_channel dict
    track1: info property output_routing_channel dict
    track1: info property arm bool
    track1: info property can_be_armed bool
    track1: info property can_be_frozen bool
    track1: info property can_show_chains bool
    track1: info property color int
    track1: info property color_index long
    track1: info property current_input_routing unicode
    track1: info property current_input_sub_routing unicode
    track1: info property current_monitoring_state int
    track1: info property current_output_routing unicode
    track1: info property current_output_sub_routing unicode
    track1: info property fired_slot_index int
    track1: info property has_audio_input bool
    track1: info property has_audio_output bool
    track1: info property has_midi_input bool
    track1: info property has_midi_output bool
    track1: info property implicit_arm bool
    track1: info property input_meter_level float
    track1: info property input_routings StringVector
    track1: info property input_sub_routings StringVector
    track1: info property input_meter_left float
    track1: info property input_meter_right float
    track1: info property is_foldable bool
    track1: info property is_frozen bool
    track1: info property is_grouped bool
    track1: info property is_part_of_selection bool
    track1: info property is_showing_chains bool
    track1: info property is_visible bool
    track1: info property mute bool
    track1: info property muted_via_solo bool
    track1: info property name unicode
    track1: info property output_meter_left float
    track1: info property output_meter_level float
    track1: info property output_meter_right float
    track1: info property output_routings StringVector
    track1: info property output_sub_routings StringVector
    track1: info property playing_slot_index int
    track1: info property solo bool
    track1: info function delete_device
    track1: info function duplicate_clip_slot
    track1: info function jump_in_running_session_clip
    track1: info function stop_all_clips
    track1: info done

操作できる項目がとても多い。
個々の項目については[Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)
を参考にしてもらうしかない。

#### `get clip_slots`

    track1: clip_slots id 4 id 5 id 6 id 7 id 8 id 9 id 10 id 11

*id* が複数、リスト形式で帰ってきている。
*Track 1* のクリップスロットに割り当てられた *id* が上から順番に出力されている。

#### `get mixer_device`

    track1: mixer_device id 12

*Track 1* の子オブジェクトを取得している。
上記`get clip_slots`の例と合わせて、内容しているオブジェクトを取得すると、それに割り当てられた*id*が手に入ることがわかる。


### functionコール

一番左の`live.object`は *Track 1* の一番上のクリップが割り当てられている。
特になにかを`get`してはいないが`fire`というボタンが`prepend call`を経由して接続されている。

#### `call fire`

ボタンをクリックすると *Track 1* の一番上のクリップのキューボタンが反応する。
再生/録音/停止などの操作をこのボタンで行うことができる。
