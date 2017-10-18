---
layout : post
title  : Linux で Blackmagic Design 製品を扱う
date : 2017/10/18
lastchange : 2017-10-18 20:35:54.
tags   :
  - blackmagic design
  - Ultrastudio Mini Recorder
  - bmdtools
  - ffmepg
  - avconv
---

## 


参考:

* [lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools)
    * [Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
* [NASM](http://www.nasm.us/)
* [猫科研究所 - 今更MinGW 2009.06\(5\) nasm,yasm](http://up-cat.net/%25BA%25A3%25B9%25B9MinGW%2B2009%252E06%25285%2529%2Bnasm%252Cyasm.html)
* [x264, the best H.264/AVC encoder - VideoLAN](https://www.videolan.org/developers/x264.html)
* [Opus Codec](http://opus-codec.org/)
* [opencore-amr download \| SourceForge.net](https://sourceforge.net/projects/opencore-amr/)
    * [opencore-amr -  Browse /fdk-aac at SourceForge.net](https://sourceforge.net/projects/opencore-amr/files/fdk-aac/)


{% capture url %}{{site.github.url}}{% link _docs/Linux/images/blackmagic-minirecorder/01_desktop_video.png %}{% endcapture %}
{% assign caption = '
{{ page.lastchange }} 現在、Ver.10.9.7
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


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

