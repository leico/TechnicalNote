---
layout : post
title  : "Max for Live: pathを使って操作する"
date   : 2017/03/21
lastchange : 2017-03-21 20:22:02.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - path
---

## Max for Live から Ableton Liveを操作する方法

参考

* [Controlling Live with Max for Live Lesson 1 - Ableton](https://help.ableton.com/hc/en-us/articles/209071389-Controlling-Live-with-Max-for-Live-Lesson-1)
* [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)
* [Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)

Max for Live から Ableton Live を操作する方法を調査した結果をまとめる。
まずは **path** について。ほとんど
[Controlling Live with Max for Live Lesson 1 - Ableton](https://help.ableton.com/hc/en-us/articles/209071389-Controlling-Live-with-Max-for-Live-Lesson-1)
と同じ内容になってしまうけれど。

## Max for Live pathについて

> ## Object Path
> 
> Live objects are accessed using paths according to the Live Object Model.
> For example, the first clip in the third track can be accessed by the path `live_set tracks 2 clip_slots 0 clip`.
> Alternatively, it can be accessed via `live_set scenes 0 clip_slots 2 clip`.
> Or, if the clip is shown in the detail view, via `live_set view detail_clip`.
> 
> As you can see, different paths can point to the same Live object. 
> One of these paths is the *canonical path*.
> 
> When communicating with the Live API, no quotes are used in paths.
> List indexes start with 0.
> 
> When navigating through the object model,
> besides these absolute paths, relative paths can be used.
> These determine a subpath beginning at the current position in the object hierarchy. 
>
> ---
> 
> * [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)

書いてあるように、Live Object Modelを用いた *path* を使ってAbleton Liveのさまざまなパラメータにアクセスすることができる。
例として

`path live_set tracks 2 clip_slots 0 clip`
: 第3トラック、最初のクリップ

`live_set scenes 0 clip_slots 2 clip`.
: 第1シーン、3番目のクリップ

`live_set view detail_clip`
: 詳細表示されているクリップ

もし詳細表示されているものが3トラック目の最初のクリップだった場合、
全ての *path* は同じものを選択していることになる。

{% capture path %}{{site.github.url}}{% link _Live/images/m4l-path/01_path.png %}{% endcapture %}
{% include responsive-img.html url=path title='それぞれのパス' alt='それぞれのパスによるアクセス方法' %}

## Live Object Model


> ## Live Object Model
> 
> The accessible parts of Live are represented by a hierarchy of objects called the Live Object Model (LOM) .
> 
> The model describes the hierarchy of objects inside Live,as seen from the Max devices. 
> There are various classes of objects in the model, like Track or Clip.
> For certain objects only a single instance exists, for other multiple instances are hold in lists.
> The Live Object Model reference shows how to navigate from a number of root objects
> down a path to the particular object of interest, and what to do with it.
> 
> ---
> 
> * [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)

> ### The Live Object Model
> 
> The LOM is essentially a roadmap to each of Live's parameters that are accessible via Max for Live. 
> 
> Not all parameters are available in Live's API, so this diagram should give you an idea of what can and can't be done via Max for Live.
>
> ---
> 
> * [Controlling Live with Max for Live Lesson 1 - Ableton](https://help.ableton.com/hc/en-us/articles/209071389-Controlling-Live-with-Max-for-Live-Lesson-1)

{% capture path %}{{site.github.url}}{% link _Live/images/m4l-path/02_LOM.png %}{% endcapture %}
{% include responsive-img.html url=path %}

* [Max 7 - LOM - The Live Object Model](https://docs.cycling74.com/max7/vignettes/live_object_model)

図はLive Object Modelの全体を俯瞰したもの。各オブジェクト間の従属関係がだいたい網羅されている。
Max for LiveデバイスからAbleton Liveのパラメータのいずれかを操作したり、連動させたりする場合は、
この図の従属関係に則って *path* をつくらなければならない。

主要なところをみてみる

`live_set`
: 開いているライブセットそのもの。

`Track`
: 各トラック。`live_set`からたどる場合`live_set tracks N`という *path* になる。
Nには0以上の数字が入る。存在しないトラックにアクセスはできない。最初のトラックは0。

`Scene`
: 各シーン。`live_set`からたどる場合`live_set scenes N`になる。
こちらもNは0以上の数字。一番上のシーンが0。

`ClipSlot`
: 各種クリップ。`live_set`から`Track`経由でアクセスする場合、`live_set tracks N clip_slots M`になる。
これもMは0以上の数字。一番上のクリップが0。
`Scene`経由でアクセスする場合、`live_set scenes N clip_slots M`になる。Mは左から順に0, 1, 2...

`Device`
: 各トラックに刺さっている各種デバイス。上の例から想像する通りの *path* になる。

> ### Paths
> 
> In order to control parameters in Live, you will need to work out the best route in the LOM.
> 
> The easiest way to understand the LOM is to imagine that objects such as Clip, Track, etc. are buildings and the interconnecting lines are roads.
> 
> For example, starting at `live_set`:
> 
> "How can I get to the volume control of the MixerDevice?"
> 
> "Continue down `live_set`, turn left onto `tracks`, then turn again onto `mixer_device`".
>
> So what do these directions look like in Max-speak?
>
> `path live_set tracks 0 mixer_device volume`
> 
> ---
> 
> * [Controlling Live with Max for Live Lesson 1 - Ableton](https://help.ableton.com/hc/en-us/articles/209071389-Controlling-Live-with-Max-for-Live-Lesson-1)

{% capture path %}{{site.github.url}}{% link _Live/images/m4l-path/03_LOM_path.png %}{% endcapture %}
{% include responsive-img.html url=path %}
* [Controlling Live with Max for Live Lesson 1 - Ableton](https://help.ableton.com/hc/en-us/articles/209071389-Controlling-Live-with-Max-for-Live-Lesson-1)

この例では第一トラックの`volume`にアクセスする方法が示されている。
これをAbleton Liveの画面でみた時の画像が以下。

{% capture path %}{{site.github.url}}{% link _Live/images/m4l-path/04_path_volume.png %}{% endcapture %}
{% include responsive-img.html url=path %}

例に示したものの他、いろんなものに *path* を使って表すことができる。

## Max for Liveから操作する方法

*path* を利用して直接Ableton Liveが操作できると便利なのだが、
Max for Liveで操作するには`id`に変換する必要がある。
