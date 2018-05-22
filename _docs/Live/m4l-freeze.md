---
layout : post
title  : Max for Liveデバイスを共有する
date   : 2018/05/22
lastchange : 2018-05-22 19:19:30.
tags   :
  - Live 9
  - Live 10
  - Max for Live
  - Freeze
  - Share
---

## **サブパッチが読み込まれない** を防止する

ただ普通にMax for Liveのファイルを共有してしまうと、
サブパッチが読み込めずに動作しない。

なので **freeze** という処理をしてあげなければならない。
今回は freeze にまつわる話をまとめた。

## Freezeして依存関係をまとめる



{% capture text %}

Freezing a Max device prepares it for distribution. 
A frozen device contains files it needs to operate. 
These files might include subpatchers, audio files, 
image files, Javascript code, or third-party Max external objects. 
(When you freeze a device that contains third-party external objects, 
you can include both Windows and Macintosh versions of the external 
in your Max search path, if you have them both. 
The frozen device device will then will contain both versions, 
and will work on both platforms.) 
Max analyzes your device to find any files it uses 
(called [dependencies](https://docs.cycling74.com/max7/vignettes/live_dependencies)), 
and then combines these files with your device. 
When a frozen device is opened, the files inside the device are used, 
even if similarly named files reside on your disk in the Max 
[search path](https://docs.cycling74.com/max7/vignettes/search_path).

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/01_freeze.png %}{% endcapture %}
{% capture caption %}
{% endcapture %}
{% assign caption = caption | markdownify %}

{% capture image %} 
{% include thumbnail.html url=url caption=caption %}
{% endcapture %}
{% assign image = image | strip_newlines %}

> ## Freezing a Device
> 
> * Click the Freeze button in the device window toolbar.
>
> {{ image }}
> 
> * Choose Save from the File menu to save the device. The device is now frozen and is reloaded in Live in its frozen state.
> 
> Note that the device is not frozen immediately when you click the freeze button. 
> It is frozen when you save it. Before saving, 
> you can make further changes to the device after clicking 
> the Freeze button by clicking the Freeze button again to unfreeze. 
> When your changes are finished, click the Freeze button again, 
> then choose Save from the File menu.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Max 7 - Freezing Max for Live Devices](https://docs.cycling74.com/max7/vignettes/live_freezing)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

書いてある通り、右下のフリーズボタンを押してセーブをすると、
依存関係にあるファイルを全て1つのMax for Liveのファイルにまとめてくれる。




{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/02_patch.png %}{% endcapture %}
{% capture caption %}
今回のサンプルパッチ

```
----------begin_max5_patcher----------
870.3ocyXEzbhBCE9r9qHCmc6PBJp6rW1N8XO0q6zoCDBXZCINgfU2Nc+sug
DzlthBk0Z8BHOR36688d4km4kgC7hEqIEdfuC9EXvfWFNXfwTkgA0OOvKOZM
lEUXFlWJSvKy8FYe0xHEdAkm8fjfU1OCLXxU9i.PHr51D+pqH8uA2WOG8zob
FQY9dvZiEpMLhA.u2FlnTscbns.JIEDtJRQE7OBnKijQ4DEQ9.gGEagxu9cV
XTaVRreLME.dwQ7LucSOUHyiLXEVaglXXqH9wuAC8pr85vgUWFcpDRDz3SiO
ixXKP9oJhi6iHp8tXhr8rwwm+bwOIQzUxP8RxHOqm7VOSQVansWQYLpC5nwq
lO+3BIpiBIraB4gvbO0RujpJ+pIoxuORUrQHdK+ZEk77JZAMlxnpMtdfHMsf
TSaSH22gmLA9IRRhLJq.KELlaDGyn3mTKjhxrEt1s4FK1eB1WrZ+WDmkKRdW
1TrPlnIuikFBr96xUgnY6t0bb0uwnn+wihsAfdwv1DP3UZ4WyQu8Cfy5S7CK
xy0LZub8eVlPEfToHGbKcE4Xo8VFOOzbyGZe5iW.IUvU67z6nX0FvMzBrN.A
tVvR75iv1J2p7WrfIjaCDAU4kAiqtpu3tTxQoCc4bA82FNCQW4eBC.2cDIeN
ztPepweldIo3sQs9I3AmAA+1iki665UytnRwagZ8SvmbBE7Cra5RVYFk+min
5AttVH5zrgZKclzFl6sgZAMiGwpZBo9WMt25orz7O.WWRYIfMhRIHxTmljlp
8.fde3cUpYTNAKJ4pC5zMOhFp1XaqXZf8uOX1mJ.cgTtoMt4DDF+UW01Px.H
5BrpcKTqeEQPS9pKaibcqKqx1sPsdp3miNStgrhhIfUDohhiX.FMmp7Zo8Y3
TqmFN+RJJzF0VPSRHbWd4H1yOCZssMbknaMgGDX59ZN5xqG7CSs9koO87zfh
1MauCk5Uw8uCkdI6cpQlCSs90HCpCxtYdl9O9mSM0HfU1eernP2GCdKKpOmK
cveG7IjBEka7O2A4qGDDbeiw6NiDpqH4++hjeWPJ7TfTG.BchvA1AbdeLxlb
Dsbod6ih5AafPuj7QaMfYiLOR4h2NXFOIo5Tkri2ZIRpWMpz46kR6Bm0g1N7
7pNjGIujZxMGVg7qC+avuOI.
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/03_sub1.png %}{% endcapture %}
{% capture caption %}

サブパッチ、 **sub1.maxpat**

```
----------begin_max5_patcher----------
281.3ocoP1jZCCCDEdszoPLqcCxl3zROEceoDjbFRUHVxHIGbHj6dkFY2lMk
ZnaFX988luabFncSX.DuJdWvX23LFUJWfMmyfd0T2YUfFCri8ZzCUkVCpX2m
F6w8drKVNSc6FYkXacN1JywllMRwGyaj12XOiQ5Z0yECwqmQ57vOi4FiKy0r
HmGCnMphFmc8RNn7pdLh98nUoKBIm6UDIdc.KmJY.AnU1iv2qaNPNyoO8TKj
KcmyygpURrNWeexzKeVDmHaCgQ8aY7U+mvr94bbGEaj+WXJWCL+UIefFMORC
Z.PMLbA8gzEIfjwQBFmb9b5KUTpwVRIe.d7hYY9REkOQgXxOi9xaLsaKTV0c
.81QC8s7rx24e4Nsl5O
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}





{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/04_sub2.png %}{% endcapture %}
{% capture caption %}

サブパッチ、 **sub2.maxpat**

```
----------begin_max5_patcher----------
406.3ocuTFsaCBBEF9Z8ofvsy0HnswsWkkkETocznfAwNaZ569DPSWWqQZqa
2n4b7v+OmO33AeOXpnkVCAuBdC34cv2yyjRmvqO1CVRZyJH0lxfhFUAUACre
phnx9jw27gjlorxfhVsHL.fiB0uN8D7d+Z3MkLdmFF8P8IqU6KnFCfmJy5ko
tvACkzZJWQTLA+VLkka28oaed4fCYhxxNsNyUFOmZZ+Pc7Qee8i.GYCm9UmA
CRonsVsUfTv5oIFJFoesxr4w3GkXXmH13lZkRsuhZW.Lkv2.C.v0EBRmHWgr
wv4CZOABW3.ydI1ElgcjYH2X1nldAyFmUQ2CqLczzTIIwkQuvYEJi54EL4p3
.+mMTNEyRh92Q1DV5FwP2LwLp.KX7e+GeCUz4OGi0hFY1vdvNeG.NsMyo0JF
2za+nlHaMW8f5V7A4fOnGzmHG5mkyP+fcvm3YvGzcc9XuVPpp1Qk08EarnaN
ZqPpCSBLgLtMzLF.kzcrg5sYHxtgKU207Foc5ncULztTQNUxaXlgCesyG8+F
XqP6fC
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


これらをフリーズしてみる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/05_before.png %}{% endcapture %}
{% capture caption %}
フリーズ前のファイルサイズ
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/06_after.png %}{% endcapture %}
{% capture caption %}
フリーズ後のファイルサイズ

ファイルが大きくなっている。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



フリーズした後、サブパッチをパスが通ってないところへ移動してデバイスをロード。
サブパッチも含めてちゃんと読み込まれているのがわかる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/07_instanced.png %}{% endcapture %}
{% capture caption %}
サブパッチを移動しても問題なくロードできる
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## フリーズしたデバイスをもう一度編集する

先程動かしたサブパッチたちを元に戻し、サブパッチに変更を加えてみる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/08_undo.png %}{% endcapture %}
{% capture caption %}
サブパッチをもとに戻す。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/09_unfreeze.png %}{% endcapture %}
{% capture caption %}
フリーズを解除して
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/10_editsub2.png %}{% endcapture %}
{% capture caption %}
変更を加える

```
----------begin_max5_patcher----------
307.3ocuRFrZCCCCF9r8SgwmyJIYMskdauA69XTbRDctjXGrcJoT569rkSXk
wBMrA6h.8aY8K8YekR3k5AvxY6YuwHjqTBAkBBjwbBuULT0HrXY7JcaKnb7j
3YNXvg5uTWy91YcBW0GR0wCFnxE8HqXUZBaCF2gw7zUor2GugpuUpZ.GZU1n
n0coAPO3eUlt2MUW5jcFv5cW3jZ0xsTVisVWd5om4AoaTZHjrPd3mkRv7vUd
cVHVjh9m+WW47krxyZYmvHZAGXN.JQYznIHFMwcoChsxO.LdoPcj+SDq32Pr
49AY6KeMfurGByrs3S51+w+OyZ4czH+dZfEvEccmAi02QDHAb3gwIsIjtKAS
kpXJNGbCbVNUeTQX7Tv4mmdSbMF1rlGuptFLpdItszfy2neBvsP4gA
-----------end_max5_patcher-----------
```
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


しかし、変更を加えても反映されない。


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/11_noreflection.png %}{% endcapture %}
{% capture caption %}
変更が反映されていない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## Freezeしたデバイスをロードした時に展開されたファイルを削除する

いろいろと原因を探った結果、同名のファイルが別の場所にあることを発見した。場所は

Live 9
: _~/Documents/Max7/Max for Live Devices/(Device Name)/_

Live 10
: _~/Documents/Max8/Max for Live Devices/(Device Name)/_

である。

どうやらFreezeしたデバイスをロードするとサブパッチ等依存関係のファイルはこちらに展開されるらしい。
そしてこちらが優先して読み込まれるため、同一ディレクトリに置いてあるサブパッチがロードされないということらしい。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/12_extracts.png %}{% endcapture %}
{% capture caption %}
サブパッチは _patchers_ サブディレクトリ内にまとめられていた。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


このディレクトリ以下を消去してロードし直すと、ちゃんと変更が反映された。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/m4l-freeze/13_reflection.png %}{% endcapture %}
{% capture caption %}
変更された **sub1.maxpat** が反映されている。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

**変更したものを共有し直す場合はまたこの状態でFreezeするのを忘れないこと。**
