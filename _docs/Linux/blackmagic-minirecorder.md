---
layout : post
title  : Linux で Blackmagic Design 製品を扱う
date : 2017/10/18
lastchange : 2017-10-18 20:56:33.
tags   :
  - blackmagic design
  - Ultrastudio Mini Recorder
  - Desktop Video
  - Media Express
---

## Ultrastudio Mini RecorderをLinuxで認識させる

ひとまず、というところまでできたのでまとめる。
後で利用するSDKを見るにDeckLink製品用のものなので、DeckLink製品系でも利用できると思う。

参考:

* [Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)



## Blackmagic Design は Linux を公式にサポートしている

CGの世界でLinuxを利用していることがあるからか、驚いたことにBlackmagic製品はLinuxに対応している。
それもただ単にSDKが公開されていてユーザがアプリを作れるだけではなく、
Linux用バイナリで専用ソフトウェアもリリースされている。

しかしまぁ実際にやってみたという記事が少ない。ので動くまでを書き残しておく。
今回利用したのは [Ultrastudio Mini Recorder](https://www.blackmagicdesign.com/jp/products/ultrastudio)。




## 公式のバイナリで動作確認する

まずは Blackmagic がリリースしているソフトウェアで動作確認する。



[Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)
にいき、_Ultrastudio Mini Recorder_ で検索する。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/00_support.png %}{% endcapture %}
{% assign caption = '
おそらく **キャプチャ・再生** が選択され、ページ下部の内容が変化する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}






左のカラムから最新版の _Desktop Video_ の Linux 用を選択する。
{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/01_desktop_video.png %}{% endcapture %}
{% capture caption %}
{{ page.lastchange }} 現在、Ver.10.9.7
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



ダウンロード、及び製品登録の画面が表示される。
まだ製品登録が済んでいない場合は登録をしてダウンロードを、既に登録している場合はダウンロードのみを行う。


{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/02_download.png %}{% endcapture %}
{% assign caption = '
_Blackmagic\_Desktop\_Video\_Linux\_ver...tar.gz_ がダウンロードされる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




ダウンロードされた tar.gz アーカイブを解凍する。

```sh
tar xzvf Blackmagic_Desktop_Video_Linux_10.9.7.tar.gz 
```
{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/03_binary.png %}{% endcapture %}
{% assign caption = '
ターミナル/GUIどちらでも可能
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



今回の場合は`Blackmagic_Desktop_Video_Linux -> deb -> amd64` 内のパッケージをインストールする。

```sh
cd Blackmagic_Desktop_Video_Linux_10.9.7a2/deb/amd64/
ls
desktopvideo_10.9.7a2_amd64.deb  desktopvideo-gui_10.9.7a2_amd64.deb  mediaexpress_3.5.3a1_amd64.deb
```

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/04_install.png %}{% endcapture %}
{% assign caption = '

_mediaexpress_ 、 _desktopvideo-gui_ のインストールは _desktopvideo_ がインストールされている必要があるので

1. _desktopvideo_
2. _desktopvideo-gui_
3. _mediaexpress_

の順にインストールを行う。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





まずは Ultrastudio Mini Recorder の設定を行う。
コンピュータと接続し、_Desktop Video Setup_ を起動する。


{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/05_setup.png %}{% endcapture %}
{% assign caption = '
インストールが完了すると、 _Sound & Video_ カテゴリの中にソフトウェアを見つけることができる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




設定を行いたい製品をクリックすると設定メニューが表示される。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/06_desktopvideo.png %}{% endcapture %}
{% assign caption = '
認識されている製品が一覧表示される。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



各々の設定を行う。
今回はHDMI映像を取得したかったので _Video Input_ タブから _HDMI_ を選択して保存する。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/07_hdmi.png %}{% endcapture %}
{% assign caption = '
ここまでが設定できるとHDMI信号が取得できるようになる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


今度は _Desktop Video Setup_ を終了し、今度は _Media Express_ を起動する。
右下のセクション、 _Log and Capture_ タブを選択すると、ほぼリアルタイムの映像信号を確認することができる。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/08_capture.png %}{% endcapture %}
{% assign caption = '
実際に映像を取得できているか確認する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


映像が表示され、ひとまず Ultrastudio Mini Recorder が Linux で利用できるようになったことが確認できる。
