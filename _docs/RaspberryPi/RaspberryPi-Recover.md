---
layout : post
title  : Raspberry Pi:VirtualBoxを使って死んだSDカードからリカバリー
date : 2017/02/02
lastchange : 2017-06-07 20:22:34.
tags   :
  - Raspberry Pi
  - VirtualBox
  - debian
  - gparted
---

## きっかけ

家で半年くらい動いていたRaspberry PiのMicro SDが死んだ。
原因は誤って起動中にSDカードを抜いてしまったことだろう。確実に。

壊れ方がおもしろくて、カードの内容はちゃんと読める。
起動はするのだが、書き込めない。ファイルの更新とかが一切反映されない。

不幸中の幸い、ある時点までのデータは読めるのでこれを元にバックアップを取り、
新しいMicro SDにコピーすることにした。

参考

* [MacでRaspberry PiのSDカードをハードコピー（バックアップ）](http://karaage.hatenadiary.jp/entry/2015/06/09/080000)
* [5 Seasons: Macでddコマンド。](http://5-seasons.blogspot.jp/2010/05/macdd.html)
* [Raspberry Piで大きな容量のSDカードから小さな容量のSDカードへまるごとコピーする](http://myboom.mkch.net/modules/pukiwiki/171.html)
* [VirtualBox &#124; saito's memo](http://memo.saitodev.com/home/virtualbox/)
* [Fix the apt-get install error: “Media change: please insert the disc labeled ...” on your Linux VPS](https://www.velocihost.net/clients/knowledgebase/29/Fix-the-apt-get-install-error-Media-change-please-insert-the-disc-labeled--on-your-Linux-VPS.html)
* [SourcesList - Debian Wiki](https://wiki.debian.org/SourcesList)

## リカバリー方法

Raspberry Piのデータを抜き出して新しいMicro SDにコピーするのだが、
新しいMicro SDの容量が元のMicro SDより少しでも小さいとコピーできない。

コピーするためにMicro SDのデータに作られているパーティションのサイズを変更しなければならない。

パーティションを変更する方法として、VirtualBox VM上のDebianでGPartedを動かすことにした。

VirtualBoxでRaspberry PiのSDを読めるようにしなければならないため、
途中SDから抜き出したデータをVirtualBoxのディスクデータに変換しなければならない。

また、復元先のMicro SDに正常に書き込めるように、復元先と同じサイズかそれ以下のディスクイメージを作る必要がある。
もちろん、こちらもVirtualBox用ディスクデータから変換する。

結果、ざっくりとした手順はこうなる。

1. Micro SDからRaspberry PiのRAWデータを抜き出す
    * `dd`コマンドで抜き出す
1. Raspberry PiのRAWデータをvdiファイルに変換する
    * `VBoxManage`を使う
1. 復元先のMicro SDのイメージを用意する
    * こちらも空の状態で一旦`dd`で抜き出した
1. 復元先のデータをvdiファイルに変換する
1. VirtualBoxにDebianを用意する
    * osboxes.orgのvdiデータを利用した
    * Raspberry Pi、復元先のディスクをDebianに追加する
1. Debianにgpartedをインストールする
    1. `/etc/apt/sources.list`を変更する
    1. Debianを最新の状態にする
    1. gpartedをインストールする
1. Raspberry Piのパーティションサイズを変更する
1. Raspberry Piのパーティションを復元先のディスクにコピーする
1. 復元先のサイズにパーティションを拡張する
1. 復元先のvdiファイルをディスクイメージに戻す
1. 復元先のディスクイメージをMicro SDに書き込む
    * こちらも`dd`コマンドを用いる

## Micro SDからRaspberry PiのRAWデータを抜き出す

まずはRaspberry Pi のMicro SDがどこに割り当てられているか確認する。
確認方法は、Macでは`diskutil list`が利用できる。

```
diskutil list
---
/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *7.9 GB     disk2
   1:                 DOS_FAT_32 boot                    78.6 MB    disk2s1
   2:                      Linux                         7.9 GB     disk2s3
```

今回の場合`/dev/disk2`がRaspberry PiのMicro SDだった。

ディスク番号が判明したところで自動マウントされている`boot`をアンマウントする。

```
diskutil unmount /Volumes/boot
Volume boot on disk2s1 unmounted
```

アンマウントできたので、データを吸い出す。


> #### イメージ読み出し
> 
> 以下は/dev/disk2がSDドライブの場合の例。以下のコマンドでSDカードの中身をRPi.imgというイメージファイルに読み出します。
> 
> ```
> $ sudo dd if=/dev/disk2 of=~/RPi.img
> ```
> 
> ---
> 
> * [MacでRaspberry PiのSDカードをハードコピー（バックアップ）](http://karaage.hatenadiary.jp/entry/2015/06/09/080000)

> でも、Linux君達とは少し違うところもあるようで、
> 
> ```
> Ritsu:Desktop tetsu$ dd if=/dev/zero of=test bs=1M count=10
> dd: bs: illegal numeric value
> ```
> 
> と怒られてしまいました。(；´Д｀)
> 
> ---
> 
> 10MBのデータファイルを作成する場合は、
> 
> ```
> % dd if=/dev/zero of=test bs=1024000 count=10 
> 
> 10+0 records in
> 10+0 records out
> 10240000 bytes transferred in 0.105885 secs (96708667 bytes/sec)
> ```
> 
> ---
> 
> * [5 Seasons: Macでddコマンド。](http://5-seasons.blogspot.jp/2010/05/macdd.html)

ということなので、こうする。

```
dd if=/dev/rdisk2 of=~/backup.img bs=1024000
```

__/dev/disk2__ ではなく __/dev/rdisk2__ にしている。ディスク番号の前に __r__ をつけると
Raw状態で読み書きができるようになる。 __r__ なしの場合、OS側が安全に読み書きできるように
余分な処理を入れるため遅い。

## 小さいMicor SDへ移行するための加工

> 今回は、16GBのSDカード(実際は16GBのマイクロSDカード)から8GBのSDカードへまるごとコピーしてみました。
> もちろんコピー元のSDカードでコピー先のSDカードの容量を超えて使用しているとコピーはできません。
> 今回は、コピー元のパーティションサイズを縮小してコピー先へコピーします。
> 
> コピーするにはGpartedというフリーのパーティション編集ツールをつかいます。
> Gpartedを使い慣れている方なら以下の説明は不要かもしれません。
> 
> Raspberry PiにつながるUSBのCD/DVDなどがあればRaspberry Pi上でもできると思いますが、もってないのでパソコン上で行いました。
> パソコン上で行うには、SDカードのリード/ライトできるUSBアダプタなど2つ(ソース、ディスティネーション)必要です。
> 
> ---
> 
> * [Raspberry Piで大きな容量のSDカードから小さな容量のSDカードへまるごとコピーする](http://myboom.mkch.net/modules/pukiwiki/171.html)

検索してみると他にもRaspberry Piのパーティションを`gparted`を用いてサイズ変更している人はそこそこいる。
この人はGPartedのLive CDで直接Micro SDからMicro SDへコピーをしているが、
コンピュータにCDドライブがないので直接コピーができない。

USBメモリも予備がないのでUSBブートも諦め、今回は[VirtualBox VM](https://www.virtualbox.org/)でコピーする。

## Raspberry Piのイメージをvdiに変換する

まずはRaspberry PiのイメージをVirtualBoxが読み込める形式にしなければならない。

> RAWイメージから仮想ハードディスクイメージを作成する
> 
> ```
> VBoxManage convertfromraw sdb.img sdb.vdi --format VDI    # VDIフォーマット
> VBoxManage convertfromraw sdb.img sdb.vmdk --format VMDK  # VMDKフォーマット
> ```
> 
> ---
> 
> * [VirtualBox &#124; saito's memo](http://memo.saitodev.com/home/virtualbox/)

初めて知ったがイメージからvdiへ変換するコマンドがVirtualBoxをインストールする際に自動的に入るようだ。
便利なのでこれを使う。

```
VBoxManage convertfromraw backup.img RPi.vdi --format VDI
```

そして移行先もVirtualBox側で読み込めないとマズいことに気づいた。
なので移行先をコンピュータに接続。イメージを作り、vdi形式に変換する。

```
dd if=/dev/rdisk2 of=~/recover.img bs=1024000

---

VBoxManage convertfromraw recover.img recover.vdi --format VDI
```

## VirtualBoxで動かすOSを取得する

VirtualBox上でGparted LiveCDを動かす手もあるはずだが、
対応しているか不明=起動するのかわからない手段ではなく確実な方法で。

VirtualBox上で[Debian](https://www.debian.org/index.ja.html)を動かし、
Debianに`gparted`をインストールして実行するようにした。慣れてるOSなので。

[Ubuntuの場合、仮想マシン向けのハードディスクイメージを配布している](https://www.ubuntulinux.jp/download)
ので、Debianも仮想HDDイメージがないか探したら[OSBoxesが作成、配布しているものを見つけた。](https://www.osboxes.org/debian/)



{% capture url %}{{site.github.url}}{% link _docs/RaspberryPi/images/RaspberryPi-Recover/01_osboxes.png %}{% endcapture %}
{% assign caption = '
OSBoxesダウンロード画面


画像のところでダウンロードすることができる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





## VirtualBoxで仮想マシンを作成

別記事にまとめた。

[VirtualBoxで仮想マシンを作成]({{site.github.url}}{% link _docs/VirtualBox/VirtualBox-Create-VM.md %})

## 仮想マシンにRaspberry Piと復元先のMicro SDファイルを追加する

{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/02_virtualbox_config.png %}{% endcapture %}
{% assign caption = 'マシンを選択して、左上2番目から設定。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/03_storage_tab.png %}{% endcapture %}
{% assign caption = '
ストレージを追加する

ストレージタブに移動し、SATAコントローラを選択後、サイドバー下部のHDD追加アイコンからハードディスクを追加
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/04_select_vhdd.png %}{% endcapture %}
{% assign caption = '
MicroSDファイルを追加

先ほど作ったMicro SDのファイルをRaspberry Pi、復元先両方とも追加する。

この時に仮想マシンのディレクトリに入れておくと混乱しない。

仮想マシンは初期設定では`~/VirtualBox VMs/`にまとめられている。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/05_config_end.png %}{% endcapture %}
{% assign caption = '
終了

追加されたら終了し、Debianを起動する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## Debianの設定

ログインには

ID
: osboxes.org

PASS
: osboxes.org

を使う。

まずは管理者rootになる。

```
su root
```

この状態で`apt-get install`すると

```
Media change: please insert the disc labeled ...
```

というエラーが出る。


> Open and modify the file /etc/apt/sources.list
> 
> ```
> vi /etc/apt/sources.list
> ```
> 
> You will see an output like this:
> 
> ```
> #
> 
> deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 CD Binary-1 20130504-14:44]/ wheezy main
> 
> deb http://ftp.us.debian.org/debian/ wheezy main
> deb-src http://ftp.us.debian.org/debian/ wheezy main
> 
> deb http://security.debian.org/ wheezy/updates main
> deb-src http://security.debian.org/ wheezy/updates main
> 
> # wheezy-updates, previously known as 'volatile'
> deb http://ftp.us.debian.org/debian/ wheezy-updates main
> deb-src http://ftp.us.debian.org/debian/ wheezy-updates main
> ~
> ```
> 
> This file contains all your package sources.
> You might find a deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 CD Binary-1 20130504-14:44]/
> wheezy main line indicating a local CDROM as a package source.
> Comment it out by placing a # symbol at the beginning of the line and save the file.
> 
> You should leave it like this:
> 
> ```
> #
> 
> # deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 CD Binary-1 20130504-14:44]/ wheezy main
> 
> deb http://ftp.us.debian.org/debian/ wheezy main
> deb-src http://ftp.us.debian.org/debian/ wheezy main
> 
> deb http://security.debian.org/ wheezy/updates main
> deb-src http://security.debian.org/ wheezy/updates main
> 
> # wheezy-updates, previously known as 'volatile'
> deb http://ftp.us.debian.org/debian/ wheezy-updates main
> deb-src http://ftp.us.debian.org/debian/ wheezy-updates main
> ~
> ```
> 
> ---
> 
> * [Fix the apt-get install error: “Media change: please insert the disc labeled ...” on your Linux VPS](https://www.velocihost.net/clients/knowledgebase/29/Fix-the-apt-get-install-error-Media-change-please-insert-the-disc-labeled--on-your-Linux-VPS.html)

`sources.list`をみると、他にもなんか少ない。

## Example sources.list

> Below is an example of a `sources.list` for Debian 8/Jessie.
> 
> ```
> deb http://httpredir.debian.org/debian jessie main
> deb-src http://httpredir.debian.org/debian jessie main
> 
> deb http://httpredir.debian.org/debian jessie-updates main
> deb-src http://httpredir.debian.org/debian jessie-updates main
> 
> deb http://security.debian.org/ jessie/updates main
> deb-src http://security.debian.org/ jessie/updates main
> ```
> 
> If you also want the contrib and non-free components, add contrib non-free after main:
> 
> ```
> deb http://httpredir.debian.org/debian jessie main contrib non-free
> deb-src http://httpredir.debian.org/debian jessie main contrib non-free
> 
> deb http://httpredir.debian.org/debian jessie-updates main contrib non-free
> deb-src http://httpredir.debian.org/debian jessie-updates main contrib non-free
> 
> deb http://security.debian.org/ jessie/updates main contrib non-free
> deb-src http://security.debian.org/ jessie/updates main contrib non-free
> ```
> 
> ---
> 
> * [SourcesList - Debian Wiki](https://wiki.debian.org/SourcesList)

最終的にこのようにした。Example contrib non-free と同じ。

```
# vi /etc/apt/sources.list

deb http://httpredir.debian.org/debian jessie main contrib non-free
deb-src http://httpredir.debian.org/debian jessie main contrib non-free

deb http://httpredir.debian.org/debian jessie-updates main contrib non-free
deb-src http://httpredir.debian.org/debian jessie-updates main contrib non-free

deb http://security.debian.org/ jessie/updates main contrib non-free
deb-src http://security.debian.org/ jessie/updates main contrib non-free
```

`apt`のカタログリストをアップデートし、Debianを最新の状態にする。

```
# apt-get update
# apt-get upgrade
```

やっと、gpartedをインストールする。

```
# apt-get install gparted
```

## gpartedを使ってパーティションサイズ変更とコピー


{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/06_Activities.png %}{% endcapture %}
{% assign caption = 'Debianデスクトップ左上`Activities`からgpartedを起動する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/07_select_gparted.png %}{% endcapture %}
{% assign caption = '`gparted`で検索し、起動する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/08_authentication.png %}{% endcapture %}
{% assign caption = '
パーティション操作を行うので、管理者権限での実行を求められる。

管理者パスワードを入れて認証する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/09_boot_gparted.png %}{% endcapture %}
{% assign caption = 'gpartedが起動する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/10_select_RPi.png %}{% endcapture %}
{% assign caption = 'まずは元々Raspberry Piが入っているディスクを選択する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/11_RPi_resize.png %}{% endcapture %}
{% assign caption = '
復元先に移動できるサイズにするために、
Raspberry Pi本体のデータが入っているパーティションのサイズを変更する。

`Resize/Move the selected partition`を選択
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/12_RPi_reduction.png %}{% endcapture %}
{% assign caption = 'パーティション右端をドラッグして復元先に収まりそうなサイズに縮小する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/13_boot_copy.png %}{% endcapture %}
{% assign caption = '
変更が完了したら各々のパーティションを復元先にコピーする。

まずはbootをコピーし
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/14_select_recover.png %}{% endcapture %}
{% assign caption = '復元先を選択し' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/15_paste.png %}{% endcapture %}
{% assign caption = 'パーティションを貼り付ける。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/16_paste_boot.png %}{% endcapture %}
{% assign caption = '
貼り付けるとサイズ変更と同じ画面が表示される。

なぜかRaspberry Piのパーティションの先頭4MiB分が使われていないので、
`Free space preceding (MiB):`の部分を __4__ に変更する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/17_RPi_copy.png %}{% endcapture %}
{% assign caption = '
PasteしてRaspberry Pi本体のデータもコピーする。

Raspberry Pi本体のパーティションをコピーして
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/18_RPi_paste.png %}{% endcapture %}
{% assign caption = 'bootの後ろに貼り付ける。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/19_RPi_extend.png %}{% endcapture %}
{% assign caption = '貼り付け終了後、パーティション後部に空白がある場合、パーティションをリサイズして埋める。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/20_Apply.png %}{% endcapture %}
{% assign caption = '全ての変更を適用' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{%link _docs/RaspberryPi/images/RaspberryPi-Recover/21_Progress.png %}{% endcapture %}
{% assign caption = 'パーティションのコピーが行われて、復元先にRaspberry Piがコピーされる。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## 復元先Micro SDをvdiからディスクイメージファイルに変換する

> * 仮想ハードディスクイメージをRAWイメージに変換する
> 
> ```
> VBoxManage clonehd sdb.vdi sdb.img --format RAW
> ```
> 
> ---
> 
> * [VirtualBox &#124; saito's memo](http://memo.saitodev.com/home/virtualbox/)

こうした。

```
VBoxManage clonehd ~/VritualBox\ VMs/recover.vdi ~/recover.img --format RAW
```

## 復元先Micro SDへディスクイメージをコピーする

復元先のMicro SDをカードリーダにセットし、

```
dd if=recover.img of=/dev/rdisk2 bs=1024000
```

を実行する。
Raspberry Piに刺し、Raspberry Piが起動できれば復元終了
