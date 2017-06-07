---
layout : post
title  : VirtualBoxで仮想マシンを作成
date   : 2017/02/03
lastchange : 2017-06-07 20:23:48.
tags   :
  - macOS
  - Sierra
  - 10.12
  - VirtualBox
  - Ver.5.1
  - debian
---

## きっかけ

Raspberry Piの復旧にgpartedが必要になったので、gpartedが動くOSを仮設することにした。

## 利用バージョン


{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/00_version.png %}{% endcapture %}
{% assign caption = '
利用したVirtualBoxのバージョンは5.1.14。おそらく6ぐらいになるまでは
そんなに画面の変化はない。

6以降になっても変化が無いかも。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## VirtualBoxで仮想マシンを作成

{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/01_create_new.png %}{% endcapture %}
{% assign caption = '
左上の新規作成を押すとウィザードが起動する。

最近ウィザードって聞かなくなったような。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/02_os_select.png %}{% endcapture %}
{% assign caption = '
使用するOSに合わせてタイプ、バージョンを選択する。

VirtualBoxサイドバーに表示されるマシン名も選択する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/03_memory_size.png %}{% endcapture %}
{% assign caption = '
メモリサイズを指定する。最低限必要になるメモリサイズが初めから設定されている。

余裕があるなら画像のように多めに指定したほうがよい。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/04_vhdd_create.png %}{% endcapture %}
{% assign caption = '
HDDを作る/指定する。

既に仮想マシン用のHDDとして配布されている場合はHDDを指定するとインストールの手間がない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/05_vhdd_select.png %}{% endcapture %}
{% assign caption = 'HDDのファイル形式を指定する。`vdi`でいいんじゃないかな。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/06_variable_stable.png %}{% endcapture %}
{% assign caption = '
`vdi`形式の場合、可変サイズにすることが可能。
使った量に合わせてファイルサイズが大きくなってゆくので、実HDD/SSDの容量節約になる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/07_vhdd_place.png %}{% endcapture %}
{% assign caption = '
仮想HDDをどこに作成するか、どこから読み込むかを指定する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/08_end.png %}{% endcapture %}
{% assign caption = '
仮想マシンが作成され、サイドバーに表示されている。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## 仮想HDDが配布されている場合

{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/09_exist_vhdd.png %}{% endcapture %}
{% assign caption = '
HDD設定の際に、`すでにある既存のハードディスクファイルを使用する`を選択し、
プルダウンメニュー横のフォルダアイコンで仮想HDDを参照する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{% link _docs/VirtualBox/images/VirtualBox-Create-VM/10_select_vhdd.png %}{% endcapture %}
{% assign caption = '
この時点で`~/VirtualBox VMs/`以下に仮想マシンのフォルダが作成されているので、
ここに仮想HDDファイルをインストールすればよい。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



ここから先は通常と同じ。

