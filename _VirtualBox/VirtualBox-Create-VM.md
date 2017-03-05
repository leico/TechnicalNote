---
layout : post
title  : VirtualBoxで仮想マシンを作成
date   : 2017/02/03
lastchange : 2017-03-05 18:52:50.
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

![VirtualBoxのバージョン情報]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/00_version.png %} "VirtualBoxのバージョン情報")

利用したVirtualBoxのバージョンは5.1.14。おそらく6ぐらいになるまでは
そんなに画面の変化はない。

6以降になっても変化が無いかも。

## VirtualBoxで仮想マシンを作成

![新規作成]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/01_create_new.png %} "左上の新規作成をクリック")

左上の新規作成を押すとウィザードが起動する。

最近ウィザードって聞かなくなったような。

![OSを選択]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/02_os_select.png %} "OS選択画面")

使用するOSに合わせてタイプ、バージョンを選択する。

VirtualBoxサイドバーに表示されるマシン名も選択する。

![メモリサイズ設定]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/03_memory_size.png %} "メモリサイズの設定")

メモリサイズを指定する。最低限必要になるメモリサイズが初めから設定されている。

余裕があるなら画像のように多めに指定したほうがよい。

![仮想HDDの作成]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/04_vhdd_create.png %} "仮想HDDの作成")

HDDを作る/指定する。

既に仮想マシン用のHDDとして配布されている場合はHDDを指定するとインストールの手間がない。

![仮想HDDのファイル形式]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/05_vhdd_select.png %} "仮想HDDのファイル形式")

HDDのファイル形式を指定する。`vdi`でいいんじゃないかな。

![可変サイズか固定サイズか]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/06_variable_stable.png %} "可変サイズか固定サイズか")

`vdi`形式の場合、可変サイズにすることが可能。
使った量に合わせてファイルサイズが大きくなってゆくので、実HDD/SSDの容量節約になる。

![HDDの保存場所]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/07_vhdd_place.png %} "HDDの保存場所")

仮想HDDをどこに作成するか、どこから読み込むかを指定する。

![作成完了]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/08_end.png %} "作成完了")

仮想マシンが作成され、サイドバーに表示されている。

## 仮想HDDが配布されている場合

![HDDの保存場所]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/09_exist_vhdd.png %} "HDDの保存場所")

HDD設定の際に、`すでにある既存のハードディスクファイルを使用する`を選択し、
プルダウンメニュー横のフォルダアイコンで仮想HDDを参照する。

![HDDの保存場所]({{site.github.url}}{% link _VirtualBox/images/VirtualBox-Create-VM/10_select_vhdd.png %} "HDDの保存場所")
この時点で`~/VirtualBox VMs/`以下に仮想マシンのフォルダが作成されているので、
ここに仮想HDDファイルをインストールすればよい。

ここから先は通常と同じ。

