---
layout : post
title  : bmdtoolsをコンパイルしてBlackmagic Design製品を扱う
date : 2017/10/18
lastchange : 2017-10-21 23:30:33.
tags   :
  - blackmagic design
  - Ultrastudio Mini Recorder
  - bmdtools
  - ffmepg
  - avconv
---

## ただ録画する以外にも使えるはずだ

高品質に動画キャプチャができるのだから、インタラクティブなシステムや配信などに利用したい。
Blackmagicが公式でSDKを配布しており、マニュアルを見る限り大抵のことはSDK経由で操作可能なように見受けられる。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/00_sdk_manual.png %}{% endcapture %}
{% assign caption = '
配布されているSDKの内部にドキュメントが付属している。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

が、機能が多すぎて読解と実装で膨大な時間を消費する。
しかし映像の取り込みや送出制御程度であれば[lu-zero](https://github.com/lu-zero)氏によって公開されていてる
[bmdtools](https://github.com/lu-zero/bmdtools)が利用できそうだ。

今回は bmdtools を利用して録画してみる。


参考:

* [lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools)
    * [Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
* [Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)

## bmdtools をコンパイルする

### libavのインストール

{% capture text %}
## Setup

`bmdtools` needs a recent version of [libav](libav.org) installed. 
In order to build it make sure to have the development headers installed.

---

### Building from source

The common scenario assumes 
you want to use `x264` to encode the video and `fdk-aac` to encode the audio. 
You might use `x265` and `opus` as well but they are not supported in many container formats.

The following instructions assume you want to install everything in `/usr/local`.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

まずは`libav`を、必要なライブラリを追加した上でインストールする必要がある。
それは以下にまとめた。

* [make installしたソフトウェアをアンインストールする]({{site.github.url}}{% link _docs/Linux/avconv.md %})

### Blackmagic Desktop Video SDK のダウンロードと解凍

bmdtools のコンパイルに欠かせない Blackmagic 公式の SDK を
[Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)
からダウンロードする。



{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/01_download_sdk.png %}{% endcapture %}
{% capture caption %}
**capture and playback** 製品を選択すると、左カラムの中に用意されている。

{{ page.lastchange }} 現在、10.9.5
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/02_resistration.png %}{% endcapture %}
{% capture caption %}
ダウンロードには登録と利用規約に同意する必要がある。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/03_agreement.png %}{% endcapture %}
{% capture caption %}
同意するボタンの色が薄くなっていて選択できないような印象を受けるが、ちゃんとクリックできる。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


ダウンロード完了したらSDKを解凍する。

```sh
$ unzip Blackmagic_DeckLink_SDK_10.9.5.zip
```



### bmdtoolsのコンパイル、インストール

{% capture text %}
##### Build

```sh
# git clone git://github.com/lu-zero/bmdtools
# cd bmdtools
# make SDK_PATH=<path where you unpacked the decklink sdk>/<Target OS>/include
```

Note: The SDK currently supports Linux and MacOSX. 
Thus the `<Target OS>` can be either `Linux` or `Mac`.

##### Install

```sh
# cp bmdcapture bmdplay /usr/local/bin
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



まずは`git clone`でソースコードを取得する。

```sh
$ git clone https://github.com/lu-zero/bmdtools.git
```

Blackmagic SDKの中から _include_ ディレクトリを bmdtools ディレクトリにコピーする。

```sh
$ cp -r ../Blackmagic\ DeckLink\ SDK\ 10.9.5/Linux/include/ .
```

`make SDK_PATH=include`でビルドする。

```sh
sudo make SDK_PATH=include install
mkdir -p //usr/bin
cp bmdcapture bmdplay bmdgenlock //usr/bin
```
## 動作確認

ひとまず、`avconv`を使って録画してみた。
```sh
$ bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -o strict=experimental:syncpoints=none -f pipe:1 | avconv -vsync passthrough -y -i - -vcodec libx264 test.avi
```

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/04_capture.png %}{% endcapture %}
{% capture caption %}
デスクトップを撮影したものを再生している。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

