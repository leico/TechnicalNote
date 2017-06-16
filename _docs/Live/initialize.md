---
layout : post
title  : "Max for Live: 値の初期化"
date   : 2017/04/02
lastchange : 2017-06-17 00:26:49.
tags   :
  - Max for Live
  - init
  - initialize

---

## 初期化が行われるタイミング

Max for Live において初期化が行われるタイミングはいつだろう。

Liveセットを開いた時・・・は、初期化ではなく前回のパラメータが読み込まれるのが望ましい。
初期値が設定されてしまうのはマズい。

となると、デバイスをトラックにインストールした時となる。

## *Initial Enable* インスペクタ

参考

* [Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)

{% capture text %}
> ## Setting an initial state for a Live UI object
> 
> * Select the Max for Live UI object and click the Inspector button 
> in the Patcher toolbar to show the object's [Inspector](https://docs.cycling74.com/max7/vignettes/inspector).
> * Click the Parameter tab at the top of the inspector window to show the Parameter attributes.
> * Ensure that Parameter Mode Enable is checked. (It will be turned on by default for all Live UI objects that store values.)
> * Enter a floating-point value for the Initial Value attribute.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Device Parameters in Max for Live](https://docs.cycling74.com/max7/vignettes/live_parameters)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



これ古い。


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initialize/01_Initial.png %}{% endcapture %}
{% assign caption = '
利用できるのはUIオブジェクトと`pattr`。

まずは *Parameter Mode Enable* にチェックをいれ、
*Parameter* インスペクタ内の *Initial Enable* をチェックすると初期化が有効になる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initialize/02_Value.png %}{% endcapture %}
{% assign caption = '
*Initial Enable* インスペクタのすぐ下、*Initial* に初期値を入力する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


Max UIオブジェクトとMax for Live UIオブジェクトで例を示した。
こうみるとMax for Live側の *Parameter* インスペクタ色々設定できるのが分かる。

なにを行うパラメータなのか全然わからないが。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initialize/03_load.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    340.3ocwRssaBCCC84zuhJ+b2DsiAa6WAgpRAOHn1jJmzJPn9uubq.aiIwjF
    ZujVe7w1GcrOlvfJ0dTCouktHkwNlvXdHG.KFyfF99U0bsmFH6ZpPBxBo54j
    j2fWjYQ9xwjpNSMZLGZwv..HKEp3xMP5xHEaMBokju24QvVtY0VgbSIgqLgR
    yml+3jrzYEt2mm3dKrHm5SKmrxvfTIJ4U03ksyNifR7CoHhJV6EspZ2C4uNp
    Xs4PnV3DBuGWWxMFRT0Yvy+oidTzjbVQcGpdeDdD+RsUqja9Q65SL0aUj41n
    N5uOckbBovH30e2TtFqnSO4jq9ER8BsXrKg7CIIm+3eGRx9ititCGQEubGug
    l++cCcqGP+9qmaak6cPf211ijVnjd46jtciuSQtv4Y9PgLD5mFPnq+A9dKE3
    jcmYrKrNJH78ylBgRUqQR1IhRwN4gjO.OZCLYC
    -----------end_max5_patcher-----------

本当に初期値が入るのか実験してみた。

### `number` について

左から

1. *Initial Enable* 無効
2. *Initial Enable* 有効、初期値 `100`

### デバイスについて

左から

1. *Initial Enable* を有効にする前にロードしていたもの
2. *Initial Enable* 有効後にロードしたもの
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

初期値が設定されているのが確認できた。


## 初期値と保存されたパラメータ

初期値が設定された状態から値を変更して保存、再読み込みを試みる。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initialize/04_store.png %}{% endcapture %}
{% assign caption = '値を変更して保存。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/initialize/05_load.png %}{% endcapture %}
{% assign caption = '再読み込み。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

初期値ではなく変更した値が優先される。予想どおりかつ利用しやすい挙動。
