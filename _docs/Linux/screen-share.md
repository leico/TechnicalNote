---
layout : post
title  : Macの画面共有(Screen sharing)とGNOME VinoでVNC接続する
date : 2017/10/18
lastchange : 2017-10-18 17:26:55.
tags   :
  - screen sharing
  - VNC
  - Vino
  - gnome
  - Ubuntu
  - Linux Mint
---

## リモートコンピュータのソフトウェアが、このバージョンの画面共有と互換性がないようです。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/screen-share/00_no-compatible.png %}{% endcapture %}
{% assign caption = '
Marvericks 以降くらいからこのように言われて LinuxのVNCと接続ができない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

これをどうにかする。ほとんど参考先のURLと同じ

参考:

* [MacからUbuntuへリモートデスクトップ - Qiita](https://qiita.com/kyo-bad/items/0d5963e14c675a0daa4b)
* [デスクトップUbuntuにVNC接続。ついでにSSHローカルポートフォワードの復習。 \| Goldstine研究所](https://blog.mosuke.tech/entry/2015/08/13/000440/)



## 暗号化を解除する

{% capture text %}
ちょっと詳細な意味を把握していないのですが、
下記を実行しないとMacで接続すると「互換性のないバージョンです」的なこといわれました…すいません。

```sh
$ gsettings set org.gnome.Vino require-encryption false
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[デスクトップUbuntuにVNC接続。ついでにSSHローカルポートフォワードの復習。 \| Goldstine研究所](https://blog.mosuke.tech/entry/2015/08/13/000440/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これを設定しなければならないということは、Macの画面共有はデスクトップ画像の情報を暗号化なしで通信する前提になっているということか。


## Linux側の設定

ほとんどは参考サイトと同様

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/screen-share/01_desktopsharing.png %}{% endcapture %}
{% assign caption = '
desktop sharing setting から
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/screen-share/02_vino_config.png %}{% endcapture %}
{% assign caption = '
有効にして設定を行うだけ
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Vinoはこれ以上細かな設定できないのだろうか。
