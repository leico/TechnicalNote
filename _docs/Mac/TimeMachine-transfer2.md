---
layout : post
title  : Catalina で 1TB → 4TB の TimeMachine へHDDを移行する
date   : 2020/07/17
lastchange : 2020-07-17 17:10:13
tags   :
  - Mac
  - Macbook
  - OSX
  - macOS
  - Catalina
  - Time Machine
  - linux
  - 起動ディスクユーティリティ
  - T2 チップ
---

## Disk Utility を使っても 1TB までしか拡張されない

Catalina にアップグレードしたのを期に、TimeMachine のディスクを 4TB のものに換装しました。
換装の際、前回の

[Time Machine 用 HDD を移行しようとして時間を無駄にしつつ解決するまで]({{site.github.url}}{% link _docs/Mac/TimeMachine-transfer.md %})

この方法でバックアップを引き継ごうとしたのですが、どういうわけか 1TB までしかパーティションサイズが大きくなりません。
今回はこれを解決します。

参考:

* [異なるサイズのディスクイメージを dd で書き出した場合のリカバリー方法（GPT篇） - Qiita](https://qiita.com/Masaaki_Komatsu/items/3d68d6cdc8f368793945 "異なるサイズのディスクイメージを dd で書き出した場合のリカバリー方法（GPT篇） - Qiita")
* [【Mac】見えなくなった107GBパーティションを復活させる方法　（その２）　UbuntuのGPartedを使う \| Storage-X](https://storage-x.com/mac/mac-partition02/ "【Mac】見えなくなった107GBパーティションを復活させる方法　（その２）　UbuntuのGPartedを使う \| Storage-X")
* [起動セキュリティユーティリティについて - Apple サポート](https://support.apple.com/ja-jp/HT208198#open "起動セキュリティユーティリティについて - Apple サポート")
* [Create a bootable USB stick on macOS \| Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview "Create a bootable USB stick on macOS \| Ubuntu")

必要なもの:

* 有線キーボード
* 有線マウス


## First Aid も正常終了するが大きくならない

本当に何度再起動をかけても、 First Aid をやっても、リカバリーモードで実行しても大きくなりません。
Disk Utility ではお手上げのようです。何か別の方法を探します。

{% capture text %}

実は、簡単に修復できるツールがあります。
gpartedです。
起動すると、「このディスクは、容量を目一杯使えない設定になっているが、使えるようにするか？」と、聞いてきます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[異なるサイズのディスクイメージを dd で書き出した場合のリカバリー方法（GPT篇） - Qiita](https://qiita.com/Masaaki_Komatsu/items/3d68d6cdc8f368793945 "異なるサイズのディスクイメージを dd で書き出した場合のリカバリー方法（GPT篇） - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## 見えなくなった107GBパーティションが復活

ディスクユーティリティでパーティションを確認する。「DATA2（107.24GB）」が今回復活したパーティションだ。
なぜかRecoverry HDも作成されていた。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Mac】見えなくなった107GBパーティションを復活させる方法　（その２）　UbuntuのGPartedを使う \| Storage-X](https://storage-x.com/mac/mac-partition02/ "【Mac】見えなくなった107GBパーティションを復活させる方法　（その２）　UbuntuのGPartedを使う \| Storage-X")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

Linux の `gparted` を利用するとパーティションサイズの変更ができそうです。その準備をしていきます。

## 起動セキュリティユーティリティで Linux を起動できるようにする

Catalina からセキュリティが強化され、通常の状態では Linux などを外部ディスクから起動することができなくなっています。

{% capture text %}

# 起動セキュリティユーティリティについて

**起動セキュリティユーティリティを使えば、いつでも指定した起動ディスクから、正規の信頼されたオペレーティングシステムを使って Mac が起動するように徹底できます。**

[Apple T2 セキュリティチップを搭載した Mac コンピュータ](https://support.apple.com/ja-jp/HT208862)
にだけ付属している起動セキュリティユーティリティは、
3 つの機能 (
[ファームウェアパスワードによる保護](https://support.apple.com/ja-jp/HT208198#firmwarepassword) 、
[安全な起動](https://support.apple.com/ja-jp/HT208198#secureboot) 、
[外部起動](https://support.apple.com/ja-jp/HT208198#externalboot)
) を使って、Mac を不正アクセスから守ってくれます。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[起動セキュリティユーティリティについて - Apple サポート](https://support.apple.com/ja-jp/HT208198#open "起動セキュリティユーティリティについて - Apple サポート")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


これを解除するには起動ディスクユーティリティを利用します。

{% capture text %}

起動セキュリティユーティリティを開くには、以下の手順を実行してください。

1. Mac の電源を入れ、Apple ロゴが表示されたらすぐに「command (⌘) + R」キーを押し続けます。Mac が macOS 復元から起動します。
2. 「macOS ユーティリティ」ウインドウが表示されたら、メニューバーから「ユーティリティ」>「起動セキュリティユーティリティ」を選択します。
3. 認証を求められたら、「macOS のパスワードを入力」をクリックし、管理者アカウントを選択して、そのパスワードを入力します。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[起動セキュリティユーティリティについて - Apple サポート](https://support.apple.com/ja-jp/HT208198#open "起動セキュリティユーティリティについて - Apple サポート")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



起動ディスクユーティリティでは以下のように設定します。そうしなければ外部ディスクから Linux を立ち上げられませんでした。



{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/01_Startup_Security.jpg %}{% endcapture %}
{% capture caption %}

安全な起動
: セキュリティなしを選択

許可する起動メディア
: 外部メディアまたはリムーバブルメディアからの起動を許可を選択

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## Live USB を作成する

Linux のインストール用 USB を作成します。インストール用イメージの中の `gparted` を利用します。
[ `gparted` の Live USB も存在します](https://gparted.org/download.php "GParted -- Download")
が、こちらは起動ディスクとして表示されず macOS から起動することができませんでした。

利用する Linux は [Linux Mint Cinnamon](https://www.linuxmint.com/edition.php?id=281) にしました。

USBドライブへの書き込みは
[Create a bootable USB stick on macOS \| Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview "Create a bootable USB stick on macOS \| Ubuntu")
の方法を使いました。

まずいらない USB を FAT にフォーマットします。

{% capture text %}

* Launch Disk Utility from Applications>Utilities or Spotlight search
* Insert your USB stick and observe the new device added to Disk Utility
* Select the USB stick device (you may need to enable the option View>Show All Devices) and select `Erase` from the tool bar (or right-click menu)
* Set the format to `MS-DOS (FAT)` and the scheme to `GUID Partition Map`
* Check you’ve chosen the correct device and click `Erase`

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Create a bootable USB stick on macOS \| Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#3-prepare-the-usb-stick "Create a bootable USB stick on macOS \| Ubuntu")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/02_Erase_FAT.png %}{% endcapture %}
{% capture caption %}
フォーマット
: FAT

方式
: GUIDパーティションマップ

でフォーマットする。

**注意 : フォーマットすると USB メモリ内の全てのデータが消去される。**

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


Linux Mint の iso を転送します。転送には
[Etcher](https://www.balena.io/etcher/ "balenaEtcher - Flash OS images to SD cards & USB drives")
というソフトウェアが利用できるそうです。


{% capture text %}

Select image 
: will open a file requester from which should navigate to and select the ISO file downloaded previously. By default, the ISO file will be in your Downloads folder.

Select drive
: replaced by the name of your USB device if one is already attached, lets you select your target device. You will be warned if the storage space is too small for your selected ISO.

Flash! 
: will activate when both the image and the drive have been selected. As with Disk Utility, Etcher needs low-level access to your storage hardware and will ask for your password after selection.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Create a bootable USB stick on macOS \| Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#5-etcher-configuration "Create a bootable USB stick on macOS \| Ubuntu")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/03_Etcher.png %}{% endcapture %}
{% capture caption %}
iso イメージと転送先の USB ドライブを選択する。

**選択ミスに注意する**
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/04_admin_pass.png %}{% endcapture %}
{% capture caption %}
管理者パスワードを要求される
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/05_Write.png %}{% endcapture %}
{% capture caption %}
書き込みが開始される
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/06_Varidate.png %}{% endcapture %}
{% capture caption %}
書き込み後、内容の検証が行われて
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/07_Done.png %}{% endcapture %}
{% capture caption %}
書き込み完了する
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

起動できる状態になったのでコンピュータを再起動し、 Linux Mint を立ち上げます。

**Linux Mint では macbook のキーボードやトラックパッド、純正マウスを利用できません。
何かしらの有線マウスと有線キーボードが必要です。**

## `gparted` を立ち上げる

先程の USB とTimeMachine ドライブをコンピュータと接続した状態で、起動時に `option` キーを押し、
起動ディスクを選択します。選択する起動ディスクは一番右の _EFI Boot_ です。
中央の _EFI Boot_ では起動に失敗しました。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/08_EFI_Boot.jpg %}{% endcapture %}
{% capture caption %}
一番右が成功。中央は起動しない
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Live USB は互換モードで立ち上げます。2番目の項目を選択します。こちらも通常起動はできませんでした。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/09_compatibility_mode.jpg %}{% endcapture %}
{% capture caption %}
一番右が成功。中央は起動しない
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

起動すると大きな解像度で起動します。解像度の変更は現時点ではできませんでした。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/10_huge.jpg %}{% endcapture %}
{% capture caption %}
画面がとても大きい。中央が `gparted`
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ターミナルから `gparted` と入力し、 `gparted` を起動します。
`gparted` が起動した瞬間から、最初の方で言及されていたパーティションサイズの修正を勧められます。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/11_expand.jpg %}{% endcapture %}
{% capture caption %}
_/dev/sda_ のサイズがおかしいようだ。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`fix` を押してサイズを修正の後、空き領域に新たなパーティションを作ります。
右上から TimeMachine のドライブを選択します。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/12_select_drive.jpg %}{% endcapture %}
{% capture caption %}
間違いがないかパーティションのサイズと内容を確認する。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

間違いがなさそうであれば空き領域を選択後、左上の create new partition ボタンを押します。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/13_create_partition.jpg %}{% endcapture %}
{% capture caption %}
空き領域を確実に選択して create new partition を押す
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ダイアログが表示されます。空き領域が全て選択されていれば大丈夫です。
フォーマットタイプは Linux 用パーティションがよさそうです。 macOS 用パーティションを作成したら後でうまくいきませんでした。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/14_ext4.jpg %}{% endcapture %}
{% capture caption %}
新しい領域を作成する。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ここで作成した領域は macOS 側の Disk Utility で削除します。
削除することで TimeMachine のパーティションサイズをディスク全体に拡げます

パーティションを作成したら、中央の apply ボタンを押します。

**Apply すると元に戻せないため、もう一度ドライブとパーティション構成を確認します。**

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/15_apply.jpg %}{% endcapture %}
{% capture caption %}
Apply するまでは元のパーティション構成のまま
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/16_alert.jpg %}{% endcapture %}
{% capture caption %}
Apply をもう一度押すと実行される
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

専用のダイアログが顕れ、問題が起きなければ少し待った後にパーティションが作られた状態になります。
Linux での作業はここまでなので、 `gparted` を終了しメニューから再起動を行います。
次は macOS での作業です。

## Disk Utility でパーティションを拡大する

macOS に戻ったら、 TimeMachine ドライブのパーティションを変更します。
ドライブが表示されておらず、パーティションだけ表示されている場合は、
表示メニューから _全てのデバイスを表示_ します。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/17_show_device.png %}{% endcapture %}
{% capture caption %}
全てのデバイスを表示させる
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ここで TimeMachine デバイスを選択し、 First Aid を行ってみました。
現状ではエラーが表示されます。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/18_error.png %}{% endcapture %}
{% capture caption %}
First Aid は現時点ではエラー
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

TimeMachine デバイスを選択したまま、パーティションボタンを押します。
パーティションの設定画面になります。

ここで先程作ったパーティションを選択し、下の `-` ボタンを押すことでパーティションを削除します。
削除すると自動的に TimeMachine 領域がドライブ全体へと拡張されます。

拡張されたのを確認し、適用ボタンを押します。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/19_expand_partition.png %}{% endcapture %}
{% capture caption %}
先程作成したパーティションを消去し、 TimeMachine 領域を拡大する
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

問題なく終わるとこのような表示になります。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/20_expanded.png %}{% endcapture %}
{% capture caption %}
パーティション領域が最大化した
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

この状態で First Aid を行ってみます。
問題なさそうです。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/TimeMachine-transfer2/21_first_aid.png %}{% endcapture %}
{% capture caption %}
ドライブが正常になった
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

無事拡張完了しました。