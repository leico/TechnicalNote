---
layout : post
title  : Mac で USBが認識されない問題の原因、対処法
date   : 2017/03/27
lastchange : 2017-03-27 17:01:49.
tags   :
  - Mac
  - Macbook
  - OSX
  - macOS
  - Macbook Pro
  - USB
---

## とりあえずSMC/NVRAMリセット

> とりあえずその場で何とかなるなら、それに越したことはない。
> 解決法を聞いてみた。
> 
> 以下、手順。
> ってかアップルサイトに案内がある。
> 
> [Intel-based Macs：SMC (システム管理コントローラ) のリセット](https://support.apple.com/ja-jp/HT201295)
>
> [Mac の PRAM および NVRAM をリセットする](https://support.apple.com/ja-jp/HT204063)
> 
> この2種類のリセットをしてみたところ
> 無事USBポートが復活！！！
>
> ---
>
> * [MacBookのUSBポートが認識不良！ - 修復手順 \| BEAT one's way](http://beat.sakura.hippy.jp/?eid=1233860)

これで解決する可能性があります。

旧型のMacbookで以前同じような症状がでてこの方法で解決しました。

>     「電源を入れた直後、option+command+R で起動」
>
> こんなの初めて知りました（PRAMクリアのPがないバージョン）。
> 何かというと、ネットを介してメモリ上にシステムをダウンロードして、
> そこから起動するという。つまり、Macbook内のシステムは一切使用しないで立ち上がるのだ。
> 
> ---
> 
> こんな感じでシステムをダウンロードし始めます。
> これで何がわかるかと言えば、これでマウスを挿してみて不具合が出なければ、
> Macbook内のシステムが何らかの悪さをしているということ。
> これでも不具合が出れば、USBポートがハード的に壊れてる可能性があるということ
> （ちなみにこれをシャットダウンするには、電源ボタン長押しで強制終了して良いらしい。
> メモリ上に読み込まれたシステムなので、Mac本体に危害を加えることはないそうだ）。
> 
> ---
> 
> * [Macbook Air11の右のUSBポートの認識がおかしい。 - まなぶろぐ。\| デザインオフィススズキ](http://manablog.dosuzuki.com/apple/post-1314/)

こういうのもあるらしいです。


## 他、USB周辺に関する諸々

> 奥の方の USB ポートはすでにハブになっていて、 キーボード、トラックパッド、
> Bluetooth、iSight カメラと共有されているのだそうです。
> このあたりは、システムプロファイラから USB を選んで、どのデバイスが
> どの USB ツリーにぶら下がっているかをみると理解できます。
>
> コメントでもご指摘いただきましたが、MacBook Pro の場合は両側に USB がありますが、
> 向かって左側がハブになっていて（すでにいろんなデバイスがつながっているので）、
> 向かって右側の USB 方が USB パワーが強いみたいです。
> 
> [The MacBook. All USB Ports Are Not Equal \| Wired](https://www.wired.com/2008/05/the-macbook-all)
> 
> [All USB ports aren’t created equal \| ZDNet](http://www.zdnet.com/topic/apple/?p=1717)
> 
> ---
>
> * [MacBook の２つの USB ポートには違いがある...？ \| Lifehacking.jp](http://lifehacking.jp/2008/05/difference-of-macbook-usb-ports/)

---

> ネットを調べてみると、macはUSBポートによって内部機器の接続状態が異なっており、
> バスパワーで動作するUSB DACの場合には正常に動作しないポートもあるみたい。
> 内部機器が沢山ぶら下がっているポートの供給電力（バスパワー）が低下してしまうことが原因のようです。
> 
> MA1はバスパワーを必要としないのでその点問題はありませんが、
> 内部機器が沢山ぶら下がっている程ノイズが多そうなので、できればそうでないポートを使いたいものです。
>
> ---
> 
> * [どちらのUSBポートを使うべきか? - memento](http://d.hatena.ne.jp/briareos156/20130710/p1)

## やってみた

{% capture url %}{{site.github.url}}{% link _Mac/images/Mac-USB/01_13_El.png %}{% endcapture %}
{% assign caption = '
* Macbook Pro 13 inch Mid 2012
* OS X El Capitan 10.11.6
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{site.github.url}}{% link _Mac/images/Mac-USB/02_13_system.png %}{% endcapture %}
{% assign caption = '手前と奥、どちらに挿しても *USB3.0バス* に接続された' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

---

{% capture url %}{{site.github.url}}{% link _Mac/images/Mac-USB/03_15_Sierra.png %}{% endcapture %}
{% assign caption = '
* Macbook Pro 15 inch 2016
* macOS Sierra 10.12.3
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{site.github.url}}{% link _Mac/images/Mac-USB/04_15_system.png %}{% endcapture %}
{% assign caption = '
[USB-C - USBアダプタ](http://www.apple.com/jp/shop/product/MJ1M2AM/A/)経由。

どのポートに接続しても *USB3.0 バス* に接続された。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

