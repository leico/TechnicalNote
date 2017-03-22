---
layout : post
title  : "Max for Live: pathからidを生成する"
date   : 2017/03/21
lastchange : 2017-03-22 18:38:45.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - path
---

## Max for Live で制御するための加工

参考

* [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)
* [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)
* [Max 7 - live.path Reference](https://docs.cycling74.com/max7/maxobject/live.path)
Ableton Live の各種パラメータにアクセスする *path* を
そのままMax for Live では使うことができない。

ある意味当然なのだが、いちいち
`live_set track 0 mixer_device volume`
と *path* 解析していてはオーバーヘッドが大きすぎる。

ので、アクセスを要求された順番に Ableton Live 側から一意な **id** が発行される。
Max for Live 側からの制御には主にこの *id* を利用する。

## *id*

> ### Object ids
> 
> An object id identifies a particular object instance in Live like a track or a clip.
> 
> To get an id, a `live.path` object must be used to navigate to the Live object. 
> When a `live.path` object sees this Live object the first time, an id is assigned to it.
> 
> The id is only valid inside the device with the `live.path`
> and remains unchanged as long the object exists. 
> If the object is moved in Live, its id usually remains unchanged. 
> There may be exceptions if the movement is implemented as a delete/create sequence, though. 
> When an object is deleted and a new object is created at its place, it will get a new id.
> 
> An id is never reused in the scope of a Max device. 
> Ids are not stored. Therefore, after loading a saved device, 
> the `live.path` object must navigate to the object again.
> 
> An object id consists of the word "id" and a number, 
> separated by a space, like `id 3`. `id 0` refers to no object.
> In Max terms it's a list of the symbol "id" and an integer. 
> 
> ---
> 
> * [Live API Overview](https://docs.cycling74.com/max7/vignettes/live_api_overview)

* *id* はLiveオブジェクト(トラックとかクリップとか操作できるもろもろ)を一意に見分けるもの
* `live.path`によって発行される
    * 初めてアクセスするLiveオブジェクトの場合、新しい *id* が発行される
    * 次回以降は同じ *id* が参照される
* 発行された *id* は基本的に変化しない
    * デバイスを移動させたり、トラックを移動させても変化しない
    * ただし、削除されたら *id* は失効する
    * 1度発行された *id* を再利用することはない
* *id* は保存されない
    * 再度読み込んだ際、別の *id* が発行されている可能性が高い
* *id* は "id" というシンボルと数字(整数)で構成される
    * `id 3`、`id 100`など
    * Live オブジェクトが存在しない場合、`live.path` は `id 0`を返す

厄介なのは、起動毎に違う *id* が割り振られるという仕様だ。
{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-id/04_randomid.png %}{% endcapture %}
{% assign caption = 'こういう状態が普通にありうる' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## `live.path`

> `live.path` is used to navigate to Live objects 
> on which the `live.object`, `live.observer` and `live.remote~` objects operate. 
> The navigation is purely path-based and is independent of the objects 
> currently present in Live 
> (navigating to a nonexistent path will result in the message `id 0` 
> being sent out the left and middle outputs rather than an error message).
> 
> ---
> 
> * [Max 7 - live.path Reference](https://docs.cycling74.com/max7/maxobject/live.path)

> ## Live.Path
> 
> As you might remember from the last lesson, 
> the path we provide Max for Live to direct it to the 
> volume parameter of track 1's volume is:
> 
> ```
> path live_set tracks 0 mixer_device volume
> ```
>
> ---
> 
> The `live.path` object takes the directions 
> we give it and returns a unique ID number 
> (out of its second outlet) for the parameter: track 1, mixer volume.
> 
> ---
>
> * [Controlling Live with Max for Live Lesson 2 - Ableton](https://help.ableton.com/hc/en-us/articles/212086305-Controlling-Live-with-Max-for-Live-Lesson-2)

`live.path` は *path* を与えると *id* を発行してくれるM4Lオブジェクト(*id* を与えて *path* を取得することもできる)。
*path* の与え方は以下







{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-id/01_path.png %}{% endcapture %}
{% assign caption = '
*path* から *id* を取得

```
----------begin_max5_patcher----------
382.3ocqSsraBCCD7bxWgkOmhh4c6uRUExjrBLMwNx1IMHD+60dsCBZAJUhC
wQ6380r63CoIz0pdvPIuQdmjjbHMIAg7.IQ6DZMuunhaP2n0fwv2.zrvcVn2
h3hRBa.T1VqZsUfECgEQa31hsB4lUZnvFJIa1hQ4YjorW8+lk6OGyFkS9HFi
nDStZ8tWXmReH218MPHKT5I+cUVHGJ73HnwtuBckR8.GSS8GYOHgkvWtx+K9
VI5fQNJs85jdxcH87YddNY4X+ufwsH8xay4r3204N6ov8asr87l3m.qLfkX0
7hOMjbRsnGzqJgNQAP5TUs0v+WRDmNSY3bY9j6MdV7bkDXLtMq7mOIv9zie4
rxnZ0ECULttHrSUtDLVgjaEJ4Y930wm4zVQYIf2mODlvvWWAHKyu5R6QaG23
gj+GsyxK74w6lvrh2zzAZSLkXi3TP6TZu4hLzTHCl3SBp1oNF7G2FTt1oArN
APqNrR5mOkFBUUBZYq.kLo9JeL8az0+E..
-----------end_max5_patcher-----------
```
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

見ての通り、`path `に続く *path* に適応したパラメータに対する *id* を取得することができる。









tracks の数字を操作することで操作するパラメータを変更したり

{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-id/02_pak.png %}{% endcapture %}
{% assign caption = '
`pak` を用いた応用例

```
----------begin_max5_patcher----------
465.3ocsTEsaqBCC8Y3qHJOysJ.EZY+JSSUAvpMaP.kD5koo8ueSLPuqaTJS
Z6AhjcbrO9vw9MeOZdSOnojGHOR77dy2yCc4b3MZ6Qq48EUbMFFU1UmCJZvv
UVqlNSEXvKiF81xMEmDxiGTPgYH4oQrM6xxx1uOfDyh1vBHIL2YT3FF4owGJ
JwZzj+7eBynWxlhWCFPc.j77JvEBa7tghadsEFJCkFPn4b4Q5kbZgnPNgvvQ
mZyqCIhRcNd222cDrVN.9qEhS3y.8XSZA5KDaqehTINCGzfgXT7hWzDFoVza
weIbVT.jyMUc0v7TX3sovsoHgEucGxa62sH8kPuIEMO0j9iPM0fVyOBegaDk
jnueGmfBksgYqPuDshFtUAZPZ3FQi7l0gMdLOME8apfbBmMNIz7TU78EG6wN
IMYQpJltz3C9cWJ6p5sLks7PG9FamK+7dHrKc9ulG0McphI7Nsof7+RWBZiP
h38iAYmGHwWB5jnrDjebSRoP6VtfrDa1+nqFOIqAOwWEzuJdbkJ7d3I5pfVO
dF98waaOCJ8XNQnXE7O2nbl6BPSgbvDUwTkcW3T73LEkqrhZiUg0oFTI8oao
COsoDTxNAJl7cU9c++Qn+5X+
-----------end_max5_patcher-----------
```
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}







各ボタンによって全く別のパラメータを取得するなどができる
{% capture url %}{{ site.github.url }}{% link _Live/images/m4l-id/03_prepend.png %}{% endcapture %}
{% assign caption = '
`prepend` はリストの先頭に指定したシンボルをつける

```
----------begin_max5_patcher----------
479.3ocyUE0aiBCC9Y3WQTdlaJP5nk6uxoopTvZM6f.JIzQU09ueINvt161Z
oUaS6A.Yii8m+3ylCwQzMsCfgR9I4WjnnCwQQnKuinQ6HZiXnrVXvvnMfwHd
DnIg2YgAK52pEk+1PxHMxAPutB1IKARmPojpGmhV02z1aqAKlqzQucBa4VWT
q0PoMfkBd5crDR1Rt+Q5p6QKmOxCiGRVgkscyS+fylxeH418cPHMT5qw2oAC
nrBqrUcTgRYqBUJTB13sWOlCwR0DfyFcZr6qwJPodGuDG6uk7wvfomxfNPWY
HrqmAYg9JqXFLXVwLXvOApPAO6J++wDtuUct11odraI0xcvZCXuUFfmGzP7k
mkAVbsLP5mpXPVQxu5NdUA1pK349G2yNaCmc6CMKyQ0zhL1W0Ly6HT7Zi67p
j2lp3WVbTfiG4me5f+9TUx30EorvlL9J9koryqrvy35b0+t2F6Ru+S4QSaut
bBuSqKI+szUfwJUHdOJH+7vQAsUVUA36YSGSZDapAjkXu4Wz4hG+xmuU3Ywb
vC+qCO9RkdI7jcRPyGOA4jnqaGnMi4Dgha.7oVs2bYBZJUASbphpc+dZJdbF
mJztgLqSw2qCp1g7vRUZSaEnU8RTbG6q7Kw+AjSt0WL
-----------end_max5_patcher-----------
```
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

