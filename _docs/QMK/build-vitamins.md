---
layout : post
title  : "Let's Split - Vitamins included を組み立てる"
date   : 2018/08/22
lastchange : 2018-08-22 23:51:44.
tags   :
  - 自作キーボード
  - "Let's Split - Vitamins included"
  - ortholinear
---

## Let's Split - Vitamins included を買った

[Let's Split - Vitamins Included Kit – NovelKeys, LLC](https://novelkeys.xyz/collections/diy-kits/products/lets-split-vitamins-included-kit)

これを買って組み立てるまでのログ。
購入したのが 8/12 、発送通知が 8/14 、到着したのが 8/21 、送料が \$23.62 。

## 開封

割と小さな箱に特にこれといったクッション無しで届く。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/01_unpack.jpg %}{% endcapture %}
{% capture caption %}

開けてみた所

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

これが基板等を開封したところ

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/02_PCBs.jpg %}{% endcapture %}
{% capture caption %}

内容物:

ネジ類
:   
    * ネジ(長) x 6
    * ネジ(短) x 6
    * スペーサー x 6
    * 高ナット x 6
    * ナット x 6

基板
:   * 左用 x 1
    * 右用 x 1

ケース
: 
    * 表用 x 2
    * 裏用 x 2

ステッカー
: 
    * NovelKeys x 1
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 基板と表面を固定する


ケース用のアクリルには穴が5つあいているが使うのはこの3箇所になる。
アクリルを表裏ひっくり返したり、上下反対にしたりしながらどうにかして基板とネジ穴の位置をあわせる。

ネジは表面から基板に向けて長ネジを入れる。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/03_bolts.jpg %}{% endcapture %}
{% capture caption %}

ネジの場所。左右で反転するので注意。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



アクリルと基板の間、それぞれのネジにスペーサーをはさむ。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/04_spacer.jpg %}{% endcapture %}
{% capture caption %}

ネジ穴の径とまったく合ってないがこんなもんなのだろう。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




アクリル、スペーサー、基板となったところで、六角ナットで表面のアクリルと基板を固定する。
のだが穴がかなりガバガバで、締め終えた後にスイッチをつけようとしたらアクリルが干渉するなんてことが起こる。

悲しいことにならないよう、4隅にひとまずスイッチをつけた状態で位置合わせをするとよい。


{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/05_adjust.jpg %}{% endcapture %}
{% capture caption %}

Kailh 製ホットスワップソケット付きなので付けたり外したりがすごく簡単にできる。

キーキャップを取ろうとするとスイッチ毎基板から外れる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## 基板と背面を固定する

付属の高ナット3つを、長ネジの余った部分に差し込む。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/06_high_nuts.jpg %}{% endcapture %}
{% capture caption %}

高ナット

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


あとは表面と同じように、短ボルトで裏面を固定する。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/07_short.jpg %}{% endcapture %}
{% capture caption %}

見ての通りネジ頭が机と干渉するので、傾斜をつける目的を兼ねてゴム足を奥側に設置した。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## スイッチの取り付け


{% capture text %}

**__Please make sure that your pins are perfectly straight on your switches before installing. 
Failure to do so can cause the hotswap sockets to pop off. __**

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Let's Split - Vitamins Included Kit – NovelKeys, LLC](https://novelkeys.xyz/collections/diy-kits/products/lets-split-vitamins-included-kit)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

書いてあるように、まっすぐ差し込まないととんでもない曲がり方をするので端子をしっかり伸ばして、位置を確認しながら差し込むこと。
**一番手前の段だけスイッチの取り付け向きが反転するので注意する。**

正常に差し込まれるとホットスワップの裏側から端子の頭だけ確認できる。


{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-vitamins/08_plug.jpg %}{% endcapture %}
{% capture caption %}

ちょっとだけ顔を出す

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

