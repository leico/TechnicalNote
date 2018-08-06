---
layout : post
title  : 絶対に変わらないを保証するパラメータ
date   : 2018/08/06
lastchange : 2018-08-07 04:09:06.
tags   :
  - Max
  - Max for Live
  - trigger
  - message
  - int
  - float
  - const
---

## 想定状況

Maxを利用しているとこんな場面に遭遇する事がある。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/01_scene.png %}{% endcapture %}
{% capture caption %}

* いろんなタイミングで、どこかのオブジェクトに特定の値を入れなければならない
* その際、入力されるデータはマチマチだが、どんなデータがきても一定の値しか出力されてほしくない

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ここまで入り組んでいるものでなくてもよいが、ある値が確実に出力されてほしいという場所がところどころ出てくるはず。
その時に使えるおすすめなオブジェクトを、さまざまなオブジェクトを検討しながら紹介する。

## 絶対に変わらないとは

絶対に変わらないということはありえないが、しかしほぼ変更されないことを保証することはできる。
ここで目標とするのは

1. 確実に一定の値が出力される
2. 編集途中の接続ミスによる上書きを抑制する
3. ユーザの誤操作を抑制する


とする。もちろん、

1. 整数型
2. 実数型
3. メッセージ
4. bang
5. list

といった Max の主要なオブジェクトを扱えるものとする。

では各種オブジェクト毎にメリット・デメリットを見てゆく



## `int` / `float`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/02_int_float.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
775.3oc0W98SSCEEG+4t+JZ5ySR+4Xy+ULFR23BVx5sKs2hiPHwaqZ.cHOPH
DCZLFADMFkDL5Kf7GyYa5eF1daKqHq1w5Fhuzld1oeOmymy8dtcqVhSntUaj
i.+c4uGOG2pk33XlBMvE+Lmfod6FM0cXtIzvxzDgIBki9MBpMgYG7OD7OE79
FP67y89Z+WbP+s2r6YuBnuD7dNP2FnGAzu.z2CzMA5GA5iSzvgrRSDSjDKsz
IMdfAdw4rQMHQYmhj1Lhk4kUhtIoxtINiH+8ieIrqoAtIhvxSoDkrQNAoqNw
vBesTyXdVFYUeo6nTUXPHrbIIwPLz3ZkJEdobAwWuueXusNAne.nuE71.neF
71G7OA7+D36C9qCdd8243dOZ+.916IGz6Y6Azc.uN.8PftKP+APeGPeSv6F6
y9a.dawLdgCOMIrMMvnFVtXVrkGFptjGpWmFkrlbHLUqwPpFCvJxiYeJWwR2
llc52lpqiWLpAjZQcmtm+5eczowKsCaIG28ryA55Le57Wg9HgTIwpCnfjTsB
gzbEKMRqL4P5BMsBDIQuHwHqzBEkUAkeDcEtHOFswBUUX0gHaGrlHacR5pYA
KaSclyUFm4D4IeZXoNHOs0MQDj8bHrd8nhPbnfT9VCHi2oonTYp.xbkOMHk+
eFjw6ulVfLW4SCRoaFPV2kPrvYBxwGhRhrsexpIWKxDurEKExjqM7IdR211n
FWMSqMpYKeZXM6My5KL5gAg6JGFGvWcBu3LZYhXlGiGei+zPMkq.fKQS4Qhl
YKVZZpM4V5kAwLvjfNal3x.SFu8ow0mpTgfUthkFVp4CK16w9rq+3e3vJjP6
WlfNVt1MRXQxI57CB+7HGhAlk0obJLS3ktvIK64Q1Y21lrgVa3gVrPgloZdg
N7L5PmJVjFkhL7PrhGoZiJNEm33r1ntHRbRuHJb97+ppd1ITUGsIVuUqkQ1N
wuNKpAC7Vxh4d0xrGMvQOxN2QvFsrQh+QVzsCldQBl13ZGMWqckngHBlVAAF
6ZDG6fHuVoeCP0zPAC
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どちらも数値を保持することができ、初期値を引数で入力しておくことができる。
オブジェクト自体にクリックやドラッグ&ドロップに対応する挙動が定義されていないため、
誤ってクリックして何かがトリガーされてしまう、といった誤動作は起こさないが、
一方で何かの拍子に数値が入力されてしまったら上書きされてしまう。
接続ミスによってそのようなことが発生することもある。
定数を扱うには不向きと言える。






## `pack` / `pak`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/03_pack_pak.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
930.3ocyX1sTSCDEG+51mhL45RMa9ne3afO.dkvvjVVwfsa5jrEKCCyPJhCH
vUnx3fNbgEkAbbzY7FUTeXVJviga1MQVjVRZ6RwaZlc6YO+Ome6Y+HYwrYTq
31B5qpbWkGnjIyhYyjg0UXGYhZmQstcqp0r8YloV0sdcHBqli+eXXKLqeGDV
4NJOrlqMV4jere2NuhDbzjHxJGRZ+MxJqQZ29z0+BI3YjfsIAGPB1jD7ARvV
j1aPZuFq+iHAGRBdGIXOxxsmDc+6QGCqqmRB9D05yOryoau0I+7MglRGCcjT
+FJvGIqbL03yd82O6E6QB1gOp3XrlCBV0sIhEnVQc5iWnFjE4wl0vFW8QNnY
m1CVEyQRIi7Z4T.kLYOrzBeTTOulxTQiA0rtCpFDyXCH1QdPeJhrwNtnAwYN
yvhG2JyMgUY0KTvsINVBsvNWJa1vexMhyXcWkNK7dJZ6tbG5zQ2Nqe5tekD7
RR6M69qcnzUcPPkAfkPkKKjc5ZCIpRzYhnp3MOppXils6p62846RA0I+9sme
vwwUuzx3OSBn.bCRvZBP6REc5CBIAErDRdMVCigsnKQmIRRK4Qx5Pee6YgWg
j.y75FJznATRsWQdOwFOVvKz.xSJU0+F9ohnlE3UPgq1nq4JxZzWhpe8DMQm
IRT8dSTfTIJUdUIQJiRkDRtnEdCKoRzYhjxXbPJZsmrHkdYc4QpDclHo.iCR
IMLArjHlRxYhXRSdXhdqFpS5ORxwOgXf2UhuorQ49jMOz0qtMy3BCwN+I5dA
XYV9h3zytNDC8lFhrqvSBsdBR8+a.YzsEto.YhtWDjEGOfrRSL1E0WPNDPT2
rfPVpaF+6PcqiDclHxrj2BUD7ITWdksyZXW8wzK+PuBor1VyDvyLCVpwWoM7
2nHImIRK8w.snvJuxDf7xhVVfRhIHXjnUhNSjVFiqZKocdIekST1YYb8nJcK
C6uyDQEXLUXIsqppaHjbliFoRzYhjJE2rfMN16.9OebEVhD1+kwmuaSupwfH
txU4B4mA5icPrnVvnvWuPAnLUOmhRsR5oQICYnjVZTBHCk.oQIMYnT4TnjYD
8hq78lA50+WrVtRq2aoAilzESaVqI8rtXZyZsamrVq2RqOFjFzaoMFMost8l
qst8lqst8lqsjzbM+PH6FMlG54GMblpzSqmykYdobrlNHdS1Knn5Am2I1ddO
1dzSewzSKa5wOWtUA9WHPstKUXTSmHhSUdor+APbpyMm
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

整数、実数だけではなくリストの扱いもできるようになった。
しかし値の変更に対しては `int` / `float` と同等の柔軟性を持ってしまっているので、
何かの拍子に変更されてしまいやすい。クリック等が発生して、誤ったトリガーが発生することもないが、
やはり定数を扱うには不向き。



## `button` / `bangbang`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/04_button_bang.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
778.3ocyW1saSCCEG+5zmhnbcoJNoock2.d.3J.Mk15UxTicUhynSSSZIEPS
rh3hABg.DWvGCFH18nM3gwqa6w.G6jszslktjLX2jTer84uO+r8ImtVEIk13
gPWE4aKeOYIo0pHIwMEZPJpsjhs4vN8Mc4CSoC11FhHJUE8QfCIb6mr6mn9e
j5+EZvdzQ6RGMJ7G9ulY49H5nMCsE7C5n8E13O+M0+oSdwdmL5fi1XmXO12B
A6f8Pb2pEYbfCzkopIwBiVbpQnGMBWxp8g7URriFXR57PKTuEcfcHhPrNnlQ
UYMc8ZpUkAM.guz0poJ+fn4f7rsP8gDdrBlk3WAmY0kudvsW9VMAJmo.1iDK
gZnw0qTI7Q04bGvF55Z1Cdgcf1lndwFERPVc.TrVUTNcUMefRsQX3n0rd3KC
UdiTAkVFfJKmkDTpyFTfanfBrvBkGnxzYI.UiVW+f5b1KJqzaowupnyONXzp
PrJSmkjUMKOVsTeLyIoijpQmuthnA.RFMWbmeIrisIevMxQppLceRXYb15zw
zFRfNKBQlsEAg5LAoVN.IyCsgNkMH0.st7H8pfsLcVRro+uAas8HDLJUrkej
E8gLs5wOKBxR2YIQFn7tVhfOh4xYlp+RS2K5Le2YEYfhB0F0KzQsLcVRtkx2
HyywozJm6t2g5Ol5uK0+wT+eR82gUe2Qa+7CO3cT+uSC1jFr0TE0EDb7a90w
u7C755Bm0kVN27UCRSPBj.DW+xe0ZY5sDD1nU4UtVZDl5+MZv1LXMYCVkyim
7jOO4Yuk5+JZvXdUzAxgmIkYcc3ed+Iec+ntB1RIO4CUEQezAK0BlPLcukDh
FYCQ973mQN2eBgGJg1mlrtXOmNw2ciuKHCNU9tPWhEhurSLnlQCZl6dysRrr
UxpYnTXUYEWolyiRSubvNcYeLM0jmkqzpyVZ0hIswMboud.t9+unVujhZwkX
yACVA53FMctprDgKi4Cegp7lVHQSd4PJNvUrhGuvhoCK+EgktwyQjYaXi5Jh
ohYBi7rhzlo75U9KKMvHC.
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どちらもどんな値が来ても `bang` を出力してくれる。
ただし、 `button` に関しては UI が存在するので、誤ってクリックして
誤 `bang` をトリガーしかねない。
また `bangbang` も2つのアウトレットがある。
1つしかいらない時に使うのはなんか気持ち悪い。




## `loadmess`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/05_loadmess.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
860.3ocwX1sSaCCEG+51mhnbcGJNeTR1qxDZJs0hETaRURJqHDRyMiooARSb
wF6KA2rMlfswj1MShI5CygJwiwrcZodkTRKwEtIAev9729WN9Xe5lkKoVKnK
NRU4gJORoToMKWpD2DyPogsKo1xsa8ltQ7toVOnUKrerZkz+WLtaL29fs+FP
9JP94fC1AHmL3zyGz+S.48PucF02n3MZh4cdjk1tw0ehm+pONDWONcVfPKoU
QQ2Pi8BYZvaQanrxvw32okmeSbLe5fF4nPbDcV4F6E3OONyqAe9DTasGXaoN
VgfNwijPiYbqxkYOpLiPpENJxcU70fDRajoTAh2nMNclppd0bZlvjoyx7Eik
M6ksEug9zvj9MiobclHlLyFSHIhIskrjEmLbLkGmx0YhbxXwyoIrWTVoaXIO
VkqyDYkt7XkO9oTWdMT0LvsACWJxaKnUUizzJNrWK6by3JmLU45LQbgt6vkD
2JZVUSXIZqWHdkqyD4k1cGuj7VRCMjvxDgLKDzx2aBTaYG4QsZchiC7mJTp4
5u5bBFjEeMLLg7UOuc2RHGeIBE6EeVcjF8NJzuUxJFxwPdY0yyWhnp5hGUw3
nXImNOMJH8Fi2ZLkmuDwj4c4YexMxZ3FmqxmTrT546MQrIwqLLsRZfj8fjDH
g.jcu73e.j9Wd9eAx4PucAxgPu9PuOCIGBI+F58Kfbzf9aCjOfF7wCt3rubw
edEj7RH4Xn22gD5vdCaXiKFpomOtdPGetTFYQlL6wbTCExd3UJ3fzzpP0PcC
NS7qBRd0PMsuJrCKT.x9.YmAul9buKN6czm.4Xf774qTSmpi2khrzJVol44L
ALY4r3wDj7LH4sPxIPuSYQgz.Y5ePNJifRVY66yhqIuHynS84ApKKtG1jyDi
aaFg77kHRmgp24iiuzl3W7fuLX1+eNGEzIr9nTiixUqLV9FzSe774SZgNwtc
tPmBBafCm9GW4JsV1RiV7RytsXVRqWHomfkSYUyleHkUJlRZyhRFRPoIH0TT
RWFJYe+EtZe+EtZeuEtNaRqmszFESZ8YQ5pxHpxTRKxzzwtsauNNLZ3v4pRO
fas.d2sqva54m1Ti2LDut2n9mZwMjdJTL8XiNgomO0sZ5spUaEPE1ui2vvJp
xaU9ev+9K5D
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

全ての型に対応している。が、ダブルクリックで誤トリガーしてしまう可能性があるのと、
パッチ読み込み時に必ず1回以上はトリガーされてしまう問題がある。
また、 `bang` 以外の入力は受け付けないので、必ず `bang` を入力しなければならないという問題点がある。





## `message`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/06_message.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
1046.3oc0Y1saaaCFF9X6qBUcrW.I0eV8VoXnP1gMSs1RARzEoHH.QxsaCKE
nCCnXqnE8f9aPZQCP6YEqcWLbJHWFkjxNgtSdz1h1B4fnHSQ+8xuG8JpORue
6Vl8h2CmZZbciaXzp09sa0RzDugVS9bKygA60ePPpnalCwooA6fM6TdMBdOh
n89IA8uy0l1b7Hx.Lgbucwkw1zz3GmboTx8FHZ0bZm2Mfz+mBi14lI39jx9a
66tkSGCOqs.cLrEGQns.WDjnQCCiXJHFSnowIAmhiHAjv3nkHVgaKFMw8t8O
.AdlWpPYRHj.xa7f1s4G5TSRQvoD9eZiUVN77C0EvSMGP8fkpfMCsbV+zpWP
zN5hTVtP8QJkAaFRYu9IEDXf.FV.ypF2CBiv8iGEQjyr5xSjqmDB7P0hmJC1
L7zZ8ySvVN5x3g.t5y3oLXy.Jzlv3oKNAssEYiV3jxfMCmf5iS8FQHwQykIh
YzVxItrc4i9t9hbvd5wpwBTw7VJhkDU780GTt0fXVPluQoyJAFjiTxTws3aE
mLLPzW2U.UphtLp7tbTlDLDSvI2DGEzqLE.UhQzJfQVD5gSzMFgcQ+uI5x.M
UwRFZNaFnswpDyULyLz2h+Oa+5UIlpfICRqqXEh4ijxMKu5UHlpfICJ3luNr
ZCKDPJ+bpIrTELYXAtZUjERjMSRMju33JWjkpfIwot9WopwxyQeXRYvjwjFW
ac+3gCYCm+ClJdvwzr2Py9PwyOhl8thS+Zw+7LZ1Sn4GYtTdoIOXHl0ENYSC
.q3aDUFLYHMmkTCzHjN+jWQydIiS+6WdLM6HZ9eT7nOQyeAM+Mzw+Jc76oiG
yOI6czLVOOglceZ1aoY+IM6qzrellc7Y4e7rwGOMvUt1x4u5SqUXifrJYmqn
zBqUszDkAS5FgGb8eiPv+GV7fWW7aOkl8XZ9C4l277hemcxeUb3q32Z9kOKt
prEtRfuLE54HJ6GVVclUMqza9ASBmN9qebRyOkN9Dt4kcR1a41btc9DZNyQ+
2kHr3Qmd93ub1gL6Liw4ZApSeUQ4TgkKuZkgpxfIC0EXxBw2Sjbe217JRDd6
yR5z3QI8m9pkK1STiK0eaVAygQhgsTu3Sta.unSwIayVkB6x1Ud+UyZ6Ws1V
a.s40HUk1nMg1vp0FtIz1pZsA0RaQTUIsXam4ZWOofKjT15PJvBIkkFjR7nf
ZoP5PJuERJnN.n+h5HAZ2Q5unOHBz8ChKlzfpkFs9k967ZZZZ2ESZupktdus
w2q4rYdMmMyq4rYdMmMyq4rYNMmMyo4rYNMmMyo4rYNZxlUVFevt6dWbR5ju
tPU1Ritcrn6c6H9XXT4GEaetYB9tgS6eYKAIr0uPXq2XTR4Ja1ys7Gm1bXLS
3nQgStYyT9f1eCTecPvN
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Max 最強の汎用性を誇る `message` オブジェクト。全ての型に対応し、
何が入力されても同じ値を出し続けるが、クリックのよる誤トリガーの危険性と、
右アウトレットに繋いでしまった何かがトリガーされると強制的に上書きされてしまう恐怖がある。


## `trigger`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/constant/07_trigger.png %}{% endcapture %}
{% capture caption %}

```
----------begin_max5_patcher----------
1046.3oc0Y1saaaCFF9X6qBUcrW.I0eV8VoXnP1gMSs1RARzEoHH.QxsaCKE
nCCnXqnE8f9aPZQCP6YEqcWLbJHWFkjxNgtSdz1h1B4fnHSQ+8xuG8JpORue
6Vl8h2CmZZbciaXzp09sa0RzDugVS9bKygA60ePPpnalCwooA6fM6TdMBdOh
n89IA8uy0l1b7Hx.Lgbucwkw1zz3GmboTx8FHZ0bZm2Mfz+mBi14lI39jx9a
66tkSGCOqs.cLrEGQns.WDjnQCCiXJHFSnowIAmhiHAjv3nkHVgaKFMw8t8O
.AdlWpPYRHj.xa7f1s4G5TSRQvoD9eZiUVN77C0EvSMGP8fkpfMCsbV+zpWP
zN5hTVtP8QJkAaFRYu9IEDXf.FV.ypF2CBiv8iGEQjyr5xSjqmDB7P0hmJC1
L7zZ8ySvVN5x3g.t5y3oLXy.Jzlv3oKNAssEYiV3jxfMCmf5iS8FQHwQykIh
YzVxItrc4i9t9hbvd5wpwBTw7VJhkDU780GTt0fXVPluQoyJAFjiTxTws3aE
mLLPzW2U.UphtLp7tbTlDLDSvI2DGEzqLE.UhQzJfQVD5gSzMFgcQ+uI5x.M
UwRFZNaFnswpDyULyLz2h+Oa+5UIlpfICRqqXEh4ijxMKu5UHlpfICJ3luNr
ZCKDPJ+bpIrTELYXAtZUjERjMSRMju33JWjkpfIwot9WopwxyQeXRYvjwjFW
ac+3gCYCm+ClJdvwzr2Py9PwyOhl8thS+Zw+7LZ1Sn4GYtTdoIOXHl0ENYSC
.q3aDUFLYHMmkTCzHjN+jWQydIiS+6WdLM6HZ9eT7nOQyeAM+Mzw+Jc76oiG
yOI6czLVOOglceZ1aoY+IM6qzrellc7Y4e7rwGOMvUt1x4u5SqUXifrJYmqn
zBqUszDkAS5FgGb8eiPv+GV7fWW7aOkl8XZ9C4l277hemcxeUb3q32Z9kOKt
prEtRfuLE54HJ6GVVclUMqza9ASBmN9qebRyOkN9Dt4kcR1a41btc9DZNyQ+
2kHr3Qmd93ub1gL6Liw4ZApSeUQ4TgkKuZkgpxfIC0EXxBw2Sjbe217JRDd6
yR5z3QI8m9pkK1STiK0eaVAygQhgsTu3Sta.unSwIayVkB6x1Ud+UyZ6Ws1V
a.s40HUk1nMg1vp0FtIz1pZsA0RaQTUIsXam4ZWOofKjT15PJvBIkkFjR7nf
ZoP5PJuERJnN.n+h5HAZ2Q5unOHBz8ChKlzfpkFs9k967ZZZZ2ESZupktdus
w2q4rYdMmMyq4rYdMmMyq4rYNMmMyo4rYNMmMyo4rYNZxlUVFevt6dWbR5ju
tPU1Ritcrn6c6H9XXT4GEaetYB9tgS6eYKAIr0uPXq2XTR4Ja1ys7Gm1bXLS
3nQgStYyT9f1eCTecPvN
-----------end_max5_patcher-----------
```

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


**これが最強** 。 どんな入力にも一定の値を出力し、UIによる誤操作がなく、
上書きもできない。

普段は `i` `f` `b` といった特殊なキーワードで流れを制御するオブジェクトだが、
定数を引数に入れれば確実にそれを吐き出してくれる最もコンスタントなオブジェクトになる。

