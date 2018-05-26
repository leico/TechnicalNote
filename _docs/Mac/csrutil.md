---
layout : post
title  : Macに怪しげなデバイスドライバを入れたり、ドライバを改造したりする準備方法
date   : 2018/05/27
lastchange : 2018-05-27 01:32:23.
tags   :
  - Mac
  - Sierra
  - El Capitan
  - csrutil
---

## El Capitan以降セキュリティが強化されている

{% capture text %}

ステップ１でおこなったSIPの無効化について

El CapitanからSystem Integrity Protection (SIP)という機能が追加されています（“rootless” modeとも呼ばれるようです ）．
システムファイルをを書き換えるマルウェアからの攻撃に対する頑健性を向上させるために，
システムファイルの書き換えをできなくしてしまう機能です．
なのでEl Capitanにおいてシステムファイルはsudoでも書き換えられません．
今回はシステムファイルを書き換える必要がありますのでステップ１でこれを無効にしています．

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[El capitan でthunderbolt外付けGPUを使う – Tech.D-ITlab](https://tech.d-itlab.co.jp/programming/592/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記の通り、El Capitan以降はセキュリティ強化がされており、デフォルトの状態では

* _/System_ などシステムファイル系の編集が `sudo` コマンドを用いてもできない
* ベンダーの署名がなされていない、または改ざんがなされているであろう kext ファイルを読み込まない

といった状態になっている。このままでは自家製ドライバを使ったり、既存のドライバを改変して別のデバイスに流用したりができない。
これを解決する方法をまとめる。

参考:

* [Configuring System Integrity Protection](https://developer.apple.com/library/content/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html)
* [El capitan でthunderbolt外付けGPUを使う – Tech.D-ITlab](https://tech.d-itlab.co.jp/programming/592/)
* [SIPをもっと深く知ってみよう！　〜SIPは一体何を保護しているのか〜](https://qiita.com/whitefox_105/items/0b70f7a504dcb72788e6#csr_allow_apple_internal)





## `csrutil` コマンドでSIPのレベルを下げる

{% capture text %}

ステップ１：SIPを無効にする

* `Command-R`を押しながらMacを再起動
* Utilities から Terminal を起動し以下のコマンドを実行
  * `csrutil disable`
* ”SIP”を無効にしましたみたいなメッセージが表示されます
* 再起動

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[El capitan でthunderbolt外付けGPUを使う – Tech.D-ITlab](https://tech.d-itlab.co.jp/programming/592/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ひとまずEl Captian以前のセキュリティ状態にするには
リカバリモードで立ち上げて、ターミナルから`csrutil disable` でよいらしいが、
もう少し段階的に下げることができないものか、

というか`csrutil`ってどんなことをしているのかということを調べたら以下のページがヒットした。

{% capture text %}

## きっかけ

先日El CapitanにアップデートしたところXtraFinderが動かなくなり、
最終的に、リカバリーモードで

```sh
csrutil enable --without debug
```

として一部のSIPの機能を解除することで解決しました。

盲目的にセキュリティを甘くしてしまいましたが、このままだと気持ち悪いのでいろいろと調べてみました。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[El capitan でthunderbolt外付けGPUを使う – Tech.D-ITlab](https://tech.d-itlab.co.jp/programming/592/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


このページに`csrutil`がどんなことをしているのか詳細が書かれていたので引き続き引用する。

{% capture text %}

ここで、Appleが公開しているソースコードによると

```cpp
/* Rootless configuration flags */
#define CSR_ALLOW_UNTRUSTED_KEXTS	(1 << 0)
#define CSR_ALLOW_UNRESTRICTED_FS	(1 << 1)
#define CSR_ALLOW_TASK_FOR_PID		(1 << 2)
#define CSR_ALLOW_KERNEL_DEBUGGER	(1 << 3)
#define CSR_ALLOW_APPLE_INTERNAL	(1 << 4)
#define CSR_ALLOW_UNRESTRICTED_DTRACE	(1 << 5)
#define CSR_ALLOW_UNRESTRICTED_NVRAM	(1 << 6)
#define CSR_ALLOW_DEVICE_CONFIGURATION  (1 << 7)
```
とそれっぽい定義があり、ここに先ほどの値が当てはめられるようです。

ちなみに、先ほどの0x14を２進数に直すと `000010100` となり
`CSR_ALLOW_TASK_FOR_PID` と `CSR_ALLOW_APPLE_INTERNAL` の部分が1になっていることがわかります。

また、`csrutil` で設定できる内容とビット列の関係は以下の表となります。

| コマンド                | DEVICE | NVRAM | DTRACE | INTERNAL | KERNEL | PID | FS | KEXT | 16進数値 |
|-------------------------|--------|-------|--------|----------|--------|-----|----|------|----------|
| enable --no-internal    |      0 |     0 |      0 |        0 |      0 |   0 |  0 |    0 |     0x00 |
| enable                  |      0 |     0 |      0 |        1 |      0 |   0 |  0 |    0 |     0x10 |
| enable --without kext   |      0 |     0 |      0 |        1 |      0 |   0 |  0 |    1 |     0x11 |
| enable --without fs     |      0 |     0 |      0 |        1 |      0 |   0 |  1 |    0 |     0x12 |
| enable --without debug  |      0 |     0 |      0 |        1 |      0 |   1 |  0 |    0 |     0x14 |
| enable --without dtrace |      0 |     0 |      1 |        1 |      0 |   0 |  0 |    0 |     0x30 |
| enable --without nvram  |      0 |     1 |      0 |        1 |      0 |   0 |  0 |    0 |     0x50 |
| disable                 |      0 |     1 |      1 |        1 |      0 |   1 |  1 |    1 |     0x77 |


## それぞれのフラグの意味

ここからは少し予測が入りますが、それぞれのフラグが意味する内容を整理してみます。

### `CSR_ALLOW_UNTRUSTED_KEXTS`

未署名のkextの読み込み制限を解除するフラグです。

kextはKernel Extentionの略でデフォルトのカーネルを拡張する為のモジュールです。
例えば、ドライバがこれに該当します。

10.10以降からkextには署名が必須となっているようです。

### `CSR_ALLOW_UNRESTRICTED_FS`

ファイルシステムに対する制限を解除するためのフラグです。
これは有名ですね。

この制限により特定のフォルダではファイルの作成、更新はroot権限でも不可能になっています。

また、保護される場所、例外の場所はそれぞれ

`/System/Library/Sandbox/rootless.conf`

`/System/Library/Sandbox/Compatibility.bundle/Contents/Resources/paths`

で確認することができます。

### `CSR_ALLOW_TASK_FOR_PID`

`task_for_pid()` が利用できるようになるフラグです。

これは何のための関数かというと
> an inspector process now depends on the security framework 
> to authorize use of the task\_for\_pid system service which gives a process the capability to control another process.

とあるので、どうやら別のプロセスを操作するときに利用する関数みたいです。

### `CSR_ALLOW_KERNEL_DEBUGGER`

おそらく、カーネルコードの変更制限を解除するためのフラグです。
これは、`csrutil` では設定できない項目です。
カーネルをデバッグする機会はそうそうないと思うので問題ないでしょう。

### `CSR_ALLOW_APPLE_INTERNAL`

以下のコードを見る限り、SIPが有効なら基本的に1となるフラグらしいです。

```
 * rootless=0
 *    no effect
 * rootless=1
 *    csr_allow_internal = 0
```

ここが0の場合、他のフラグが0でも意味が無いのだと思われます。

### `CSR_ALLOW_UNRESTRICTED_DTRACE`

dtraceの実行制限を解除するためのフラグです。

dtraceはプロセスの動作を追跡、監視するためのコマンドです。
実際に実行してみたところ、一部の追跡コマンドは権限がないとしてはじかれました。

### `CSR_ALLOW_UNRESTRICTED_NVRAM`

nvramの実行制限を解除するためのフラグです。
nvramコマンドを通じて内容を変更できるようになります。

ここが変更できるということはcsr-active-configを変更できるということになるので、SIPの意味がほとんどなくなってしまいます。
基本的に無効にしない方がいいフラグでしょう。

### `CSR_ALLOW_DEVICE_CONFIGURATION`

調べてみましたが、何に使われているのかはよくわかりませんでした。
これは、`csrutil` では設定できない項目です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[El capitan でthunderbolt外付けGPUを使う – Tech.D-ITlab](https://tech.d-itlab.co.jp/programming/592/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということなので、デバイスドライバ系の改造には以下のコマンドが適切なようだ。

```sh
csrutil enable --without kext --without fs
```

これをリカバリモードで実行するとこのようになる

```sh
# csrutil enable --without kext --without fs
csrutil: requesting an unsupported configuration. This is likely to break in the future and leave your machine in an unknown state.
csrutil: requesting an unsupported configuration. This is likely to break in the future and leave your machine in an unknown state.
Successfully enabled System Integrity Protection. Please restart the machine for the changes to take effect.
```

この状態で再起動をして、`csrutil status`を用いてSIPの状態を確認する。

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

`Kext Signing` 及び `Filesystem Protections` が `disable` になっている。ひとまずの目的は達成したみたいだ。
