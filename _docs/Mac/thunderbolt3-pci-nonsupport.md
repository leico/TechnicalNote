---
layout : post
title  : サポートされていないPCIカードをThunderbolt3 PCI ボックスで動かす
date   : 2018/05/27
lastchange : 2018-07-03 16:04:04.
tags   :
  - Mac
  - Sierra
  - SONNET Echo Express SE I
  - Intel Gigabit CT
  - EXPI9301CT
  - csrutil
  - kextutil
  - kextload
  - kextunload
---

## 正規動作品の高スペックはいらない。値段高いし

AVB用にThunderbolt3-Ethernet変換をどうしようか散々悩んだ挙げ句、PCI拡張ボックスを購入。
しかし、動作確認済みのPCIは 10GbE 対応だったり、やたらと値段が高額だったりする。

AVB用途であれば GbE あれば問題ない。10GbE はもっと普及し、値段が落ち着いてからで問題ないので、
GbE 対応の安価なPCIカードを購入。こちらをが動作するまでをまとめる。

なお、この方法を応用すれば Apple がドライバを提供しているチップが搭載された、
別ベンダーの安価な製品を導入することも不可能ではなさそう。

**全て動作保証外の自己責任です。
システム情報を直接書き換えるため、最悪の場合macOSが立ち上がらなくなります。
書き換える前にTime Machineによるバックアップや書き換える前のファイルを別途コピーしておいて万が一の状態に備えましょう。**

ちなみに2回ほど試行錯誤の最中に _IONetworkingFamily.kext_ をTime Machineから復元させています。

参考:

* [Intel Gigabit CT Desktop Adapter \| tonymacx86.com](https://www.tonymacx86.com/threads/intel-gigabit-ct-desktop-adapter.3748/)
* [Intel Gigabit CT kext for macOS Sierra 10.12 \| trick77.com](https://trick77.com/intel-gigabit-ct-kext-macos-sierra-10-12/)
* [HP Pavilion Elite e9180t でLionが動く夢 ｜Kanameのブログ](https://ameblo.jp/ktomite/theme-10046662134.html)
* [IBM Knowledge Center - id コマンド](https://www.ibm.com/support/knowledgecenter/ja/ssw_aix_61/com.ibm.aix.cmds3/id.htm)
* [Writing a Driver for a PCI Device](https://developer.apple.com/library/content/documentation/DeviceDrivers/Conceptual/WritingPCIDrivers/pci_device/pci_device.html)
* [How to Install Kernel Extensions in Mac OS X Manually](http://osxdaily.com/2012/01/12/how-to-manually-install-kernel-extensions-in-mac-os-x/)
* [Manually installing kexts issue \| tonymacx86.com](https://www.tonymacx86.com/threads/manually-installing-kexts-issue.234552/)
* [How to manually install kexts in 10.11 from the shell? : hackintosh](https://www.reddit.com/r/hackintosh/comments/4vw9as/how_to_manually_install_kexts_in_1011_from_the/)


## Macが公式で対応しているドライバを確認する

まずは安いNICの搭載されているコントローラの型番から、Macintoshにドライバが存在するものを検索した。
NICの場合、MacintoshだとIntelからBroadcom辺りのものを利用していそうなのでこれらメーカーのコントローラが搭載されているNICを検索する。

{% capture text %}
The Intel Gigabit CT Desktop Adapter has an Intel 82574L ethernet controller on it. 
This is the same controller used in the new Mac Pros, and as such, 
Apple generously provides the kext for us. We just need to add our Ethernet controller's device id to the list of supported devices in the kext itself.

Open 

/System/Library/Extensions/IONetworkingFamily.kext/Contents/PlugIns/Intel82574L.kext/Contents/Info.plist

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Intel Gigabit CT Desktop Adapter \| tonymacx86.com](https://www.tonymacx86.com/threads/intel-gigabit-ct-desktop-adapter.3748/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記の通り、Intel 82574Lというコントローラのドライバが、Appleから提供されていることを自分のコンピュータで確かめてみる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/01_82574L.png %}{% endcapture %}
{% capture caption %}
_/System/Library/Extensions/IONetworkingFamily.kext/Contents/PlugIns_

に存在することを確認した。

kextファイルは _右クリック -> パッケージの内容を表示_ とすることで中身を表示することができる。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


ドライバがあることを確認できたので 82574L を搭載しているNIC、 EXPI9301CT を購入することにした。






## EXPI9301CTを認識しない

購入したはいいが、全然認識しない。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/02_Thunderbolt3.png %}{% endcapture %}
{% capture caption %}

SONNET Echo Express SE I は認識している。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/03_EthernetCard.png %}{% endcapture %}
{% capture caption %}

Ethernetカードを全く認識しない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/04_PCIBus.png %}{% endcapture %}
{% capture caption %}

PCI上では存在している。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どうやら EXPI9301CT が 82574L ドライバと関連づけられていないようだ。





## SIPのレベルを下げる

{% capture text %}

Open 

/System/Library/Extensions/IONetworkingFamily.kext/Contents/PlugIns/Intel82574L.kext/Contents/Info.plist

and edit the following lines:

```xml
<key>IOClass</key>
<string>Intel82574L</string>
<key>IOPCIPrimaryMatch</key>
```
1. Change `<key>IOPCIPrimaryMatch</key>` to `<key>IOPCIMatch</key>`.
2. Add your Ethernet Controller's uid to the list in the line right after the `<key>IOPCIMatch</key>` line. 
My id is `0x10D38086`. 
The simple way to find out what it is is to use Windows Device Manager. 
What you will see is something like `PCI\VEN_8086&DEV_10D3&SUBSYSâ€¦` 
Now what you need is the numbers/letters following VEN and DEV. 
These are your vender and device ID numbers. 
My Vendor ID is `8086` and my Device ID is `10D3`.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Intel Gigabit CT Desktop Adapter \| tonymacx86.com](https://www.tonymacx86.com/threads/intel-gigabit-ct-desktop-adapter.3748/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


先ほどのページ、どうやらHackintoshに関するページで、今回購入した EXPI9301CT をHackintosh側で認識させるための内容が書かれているようだ。
これに沿ってドライバ情報を編集すれば動作するみたいだが、_/System_ 以下を編集することができない。

{% capture text %}

The Hackintosh is still running in full protected mode (if enabled in Clover):

```
$ csrutil status
System Integrity Protection status: enabled.
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Intel Gigabit CT kext for macOS Sierra 10.12 \| trick77.com](https://trick77.com/intel-gigabit-ct-kext-macos-sierra-10-12/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`csrutil`を用いて
* 署名されていない、または改造した kext を読み込む
* ファイルシステムの編集を許可

以上2点の許可をしなければならない。詳細は以下にまとめた。

[Macに怪しげなデバイスドライバを入れたり、ドライバを改造したりする準備方法]({{site.github.url}}{% link _docs/Mac/csrutil.md %})

上記のページの最後、以下のコマンドをリカバリモードで実行し、
```sh
# csrutil enable --without kext --without fs
csrutil: requesting an unsupported configuration. This is likely to break in the future and leave your machine in an unknown state.
csrutil: requesting an unsupported configuration. This is likely to break in the future and leave your machine in an unknown state.
Successfully enabled System Integrity Protection. Please restart the machine for the changes to take effect.
```


結果を通常モードで確認する。


```sh
$ csrutil status
System Integrity Protection status: enabled (Custom Configuration).

Configuration:
	Apple Internal: disabled
	Kext Signing: disabled
	Filesystem Protections: disabled
	Debugging Restrictions: enabled
	DTrace Restrictions: enabled
	NVRAM Protections: enabled
	BaseSystem Verification: enabled

This is an unsupported configuration, likely to break in the future and leave your machine in an unknown state.
```


## _Intel82574L.kext_ のバックアップを作成する

いよいよ公式のドライバを書き換えるが、その前にバックアップをしておく。
デフォルトの状態の _Intel82574L.kext_ をデスクトップにコピーする。


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/05_Backup.png %}{% endcapture %}
{% capture caption %}

デスクトップにドラッグ&ドロップ

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## _Intel82574L.kext_ 内の _Info.plist_ をコピーして編集する

同様に、 _Intel82574L.kext_ 内の _Infor.plist_ をデスクトップなどにコピーして編集する。
最終的に編集が終了したものを _Intel82574L.kext_ に戻す形で変更を反映させる。

どのように編集するかというと
{% capture text %}

Open 

/System/Library/Extensions/IONetworkingFamily.kext/Contents/PlugIns/Intel82574L.kext/Contents/Info.plist

and edit the following lines:

```xml
<key>IOClass</key>
<string>Intel82574L</string>
<key>IOPCIPrimaryMatch</key>
```
1. Change `<key>IOPCIPrimaryMatch</key>` to `<key>IOPCIMatch</key>`.
2. Add your Ethernet Controller's uid to the list in the line right after the `<key>IOPCIMatch</key>` line. 
My id is `0x10D38086`. 
The simple way to find out what it is is to use Windows Device Manager. 
What you will see is something like `PCI\VEN_8086&DEV_10D3&SUBSYSâ€¦` 
Now what you need is the numbers/letters following VEN and DEV. 
These are your vender and device ID numbers. 
My Vendor ID is `8086` and my Device ID is `10D3`.


    _Info.plist_ should look like this:

    ```xml
    <key>IOClass</key>
    <string>Intel82574L</string>
    <key>IOPCIMatch</key>
    <string>0x10D38086 0x104b8086 0x10f68086</string>
    <key>IOPCISecondaryMatch</key>
    <string>0x00008086 0x00000000</string>
    <key>IOProviderClass</key>
    <string>IOPCIDevice</string>
    <key>IOResourceMatch</key>
    <string>IOKit</string>
    ```
3. Remove the lines that say `<key>IOPCISecondaryMatch</key>`,
and also remove the line `<string>0x00008086 0x00000000</string>`.

    _Info.plist_ should now look like this:

    ```xml
    <key>IOClass</key>
    <string>Intel82574L</string>
    <key>IOPCIMatch</key>
    <string>0x10D38086 0x104b8086 0x10f68086</string>
    <key>IOProviderClass</key>
    <string>IOPCIDevice</string>
    <key>IOResourceMatch</key>
    <string>IOKit</string>
    ```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Intel Gigabit CT Desktop Adapter \| tonymacx86.com](https://www.tonymacx86.com/threads/intel-gigabit-ct-desktop-adapter.3748/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


以上のようにいろいろ書いてあるがもう少し調べる。


{% capture text %}
### PCI Matching

PCI device drivers can base their property matching on the PCI configuration space registers for vendor and device ID (offsets 0x00 and 0x02), 
subsystem vendor and device ID (offsets 0x2C and 0x2E), 
and class code (offset 0x09). 
Other registers, such as revision ID and header type, 
are not available in property matching and must be examined by the probe method. 
The PCI matching dictionary keys are:

{% assign key1 = 'IOPCIMatchMatches' %}
{% capture behavior1 %}
against the primary vendor/device ID registers or the subsystem vendor/device ID registers. 
The primary IDs are checked first; if either of these doesn’t match then the subsystem IDs are checked.
{% endcapture %}

{% assign key2      = 'IOPCIPrimaryMatch' %}
{% assign behavior2 = 'Matches only against the primary vendor/device ID registers.' %}

{% assign key3      = 'IOPCISecondaryMatch' %}
{% assign behavior3 = 'Matches only against the subsystem vendor/device ID registers.' %}


{% assign key4      = 'IOPCIClassMatch' %}
{% assign behavior4 = 'Matches against the class code register.' %}

| Key               | Matching behavior                  |
| ----------------- | ---------------------------------- |
| {{ key1 }}        | {{ behavior1 | strip_newlines }}   |
| {{ key2 }}        | {{ behavior2 }}                    |
| {{ key3 }}        | {{ behavior3 }}                    |
| {{ key4 }}        | {{ behavior4 }}                    |
	
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Writing a Driver for a PCI Device](https://developer.apple.com/library/content/documentation/DeviceDrivers/Conceptual/WritingPCIDrivers/pci_device/pci_device.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





フォーラムの投稿では `IOPCISecondaryMatch`とそれに付随するIDを消去するように書かれているが、
こちらに _サブシステムID_ 及び _サブシステム製造元ID_ を記入することでも動作させることができそうだ。

_製造元ID_、 _装置ID_、 _サブシステムID_、_サブシステム製造元ID_ は 

_このMacについて -> システムレポート -> ハードウェア -> PCI_ の項目で確認することができる。





{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/04_PCIBus.png %}{% endcapture %}
{% capture caption %}
今回の場合

_製造元ID_
: 0x8086

_装置ID_
: 0x10d3

_サブシステム製造元ID_
: 0x8086

_サブシステムID_
: 0xa01f

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




これらを _Info.plist_に反映させるとこうなる。

```xml
<string>Intel82574L</string>
<key>IOPCIPrimaryMatch</key>
<string>0x10d38086 0x104b8086 0x10f68086</string>
<key>IOPCISecondaryMatch</key>
<string>0xa01f8086 0x00008086 0x00000000</string>
```

`IOPCIPrimaryMatch`、`IOPCISecondaryMatch`共に

```
0x[(Sub)SystemID][(Subsystem)VendorID]
```

となることに注意する。ここまで編集した _Info.plist_ を _Intel82574L.kext_ 内の元の位置に戻して上書きする。

## _/System/Library/Extensions_ 内のアクセス権を修復する

{% capture text %}

Type the following commands at the terminal, replacing the kext name with the one you are installing

```sh
sudo chmod -R 755 kextfile.kext
sudo chown -R root:wheel kextfile.kext
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[How to Install Kernel Extensions in Mac OS X Manually](http://osxdaily.com/2012/01/12/how-to-manually-install-kernel-extensions-in-mac-os-x/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





自分で編集した _Info.plist_の編集権限が通常と異なっているのでシステムファイルのアクセス権を修復する。
_/System/Library/Extensions_ 内のアクセス権は基本上記の設定で問題ので

```sh
sudo chmod -R 755 /System/Library/Extensions/*
sudo chown -R root:wheel /System Library/Extensions/*
```

を実行すればよい。他の記述方法として





{% capture text %}
Set modes and ownership:

```sh
chmod -R 755 /System/Library/Extensions/Awesome.kext
chown -R 0:0 /System/Library/Extensions/Awesome.kext
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[How to manually install kexts in 10.11 from the shell? : hackintosh](https://www.reddit.com/r/hackintosh/comments/4vw9as/how_to_manually_install_kexts_in_1011_from_the/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




というものも見つけた。




{% capture text %}
**id** コマンドは、指定されたユーザーのシステム識別 (ID) を含むメッセージを標準出力に書き出します。
システム ID は、ユーザーとユーザー・グループをシステムに対して識別する番号です。
システム ID は、システムがユーザーとユーザー・グループを識別するための番号です。
**id** コマンドは、該当する場合に以下の情報を書き込みます。

---

現行ユーザーのすべてのシステム識別を表示するには、次のように入力します。

```sh
id
```

id コマンドの出力は以下のフォーマットで表示されます。

```sh
uid=1544(sah) gid=300(build) euid=0(root) egid=9(printq) groups=0(system),10(audit)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[How to manually install kexts in 10.11 from the shell? : hackintosh](https://www.reddit.com/r/hackintosh/comments/4vw9as/how_to_manually_install_kexts_in_1011_from_the/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

通常のアクセス権で`id`を実行してみると

```
$ id
uid=501(leico_studio)
gid=20(staff)
groups=20(staff),
701(com.apple.sharepoint.group.1),
12(everyone),
61(localaccounts),
79(_appserverusr),
80(admin),
81(_appserveradm),
98(_lpadmin),
33(_appstore),
100(_lpoperator),
204(_developer),
395(com.apple.access_ftp),
398(com.apple.access_screensharing),
399(com.apple.access_ssh),
702(com.apple.sharepoint.group.2)
```

`sudo` 権限で実行してみると

```
$ sudo id
uid=0(root) 
gid=0(wheel) 
groups=0(wheel),
1(daemon),
2(kmem),
3(sys),
4(tty),
5(operator),
8(procview),
9(procmod),
12(everyone),
20(staff),
29(certusers),
61(localaccounts),
80(admin),
701(com.apple.sharepoint.group.1),
33(_appstore),
98(_lpadmin),
100(_lpoperator),
204(_developer),
395(com.apple.access_ftp),
398(com.apple.access_screensharing),
399(com.apple.access_ssh),
702(com.apple.sharepoint.group.2)
```

`chown -R 0:0` は`chown -R root:wheel` を `userID:groupID` で指定しただけで同じもののようだ。


## kextキャッシュを再構築する

最後にkextを再読込、キャッシュを再構築させる。

{% capture text %}

In Terminal :

```sh
sudo kextcache -i /
```

Press return, type your password (it will not show on screen), press return again and when the command has completed restart.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [P1LGRIM](https://www.tonymacx86.com/members/p1lgrim.374452/) from
[Manually installing kexts issue \| tonymacx86.com](https://www.tonymacx86.com/threads/manually-installing-kexts-issue.234552/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これを実行して、さらに念の為再起動をして EXPI9301CT 認識するか確認する。

ドライバの改造を試行錯誤する場合、ファイルを更新するたびに上記3つのコマンドを実行するようにするべきだろう。

```sh
$ sudo chown -R 0:0 /System/Library/Extensions/*
$ sudo chmod -R 755 /System/Library/Extensions/*
$ sudo kextcache -i /
kext-dev-mode allowing invalid signature -67030 0xFFFFFFFFFFFEFA2A for kext Intel82574L.kext
kext-dev-mode allowing invalid signature -67030 0xFFFFFFFFFFFEFA2A for kext IONetworkingFamily.kext
KernelCache ID: 296EE9337ED4F9600790ECD041F1F968
```

署名が不正であると警告が出るが、問題なくロードすることができる。


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/06_Success.png %}{% endcapture %}
{% capture caption %}
成功すればネットワーク環境設定に **Thunderbolt Ethernet スロット** が生まれ、接続できるようになる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



ところで、私はAVBネットワークに接続するために XCPI9301CT を購入したのだが・・・。




{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/thunderbolt3-pci-nonsupport/07_AVBNotworking.png %}{% endcapture %}
{% capture caption %}

XCPI9301CTはAVBに対応していない・・・。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

