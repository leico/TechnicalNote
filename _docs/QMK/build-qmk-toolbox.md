---
layout : post
title  : "High Sierra で QMK Toolbox を動かす"
date   : 2018/08/27
lastchange : 2018-08-28 17:43:07.
tags   :
  - 自作キーボード
  - QMK Toolbox
  - AVR
  - atmega32u4
  - avrdude
  - High Sierra
  - 10.13.5
  - 0.0.9
---

## 起動しない

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-qmk-toolbox/01_drop.png %}{% endcapture %}
{% capture caption %}

エラー終了して起動しない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


これに続くいくつかの問題をクリアし、書き込みができるまでを記録する。

参考:

* [Is it supposed to work on macOS High Sierra ? · Issue #13 · qmk/qmk_toolbox](https://github.com/qmk/qmk_toolbox/issues/13)
* [macOSのコマンドラインアプリでdylibをよろしく扱う方法 - Qiita](https://qiita.com/omochimetaru/items/21d662b8df8bce1bc5ca)
* [アプリが使うFrameworkとかを置き換える方法 - blog.niw.at](http://blog.niw.at/post/25980282368)
* [【シェルスクリプト】コマンドの有無による条件分岐 - Qiita](https://qiita.com/8ayac/items/b6b6f0a385d08659316b)
* [いい加減覚えよう。 `command > /dev/null 2>&1`の意味 - Qiita](https://qiita.com/ritukiii/items/b3d91e97b71ecd41d4ea)
* [Homebrew のインストール先を変更する - Qiita](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
* [Linux の cp コマンドで強制上書き : まだプログラマーですが何か？](http://dotnsf.blog.jp/archives/2918928.html)
* [cp コマンドでシンボリックをコピーする - Qiita](https://qiita.com/mattintosh4/items/b9a6e8a87c0cb9bbf00d)
* [Mac OS Xでライブラリの設定変更について - Qiita](https://qiita.com/itoru257/items/d36b4e3908a39bfbbb18)
* [ファイルの指定した行のみ表示する - Qiita](https://qiita.com/y_310/items/2c40e0671b89ce6c15d5)










## High Sierra の問題

この問題は以下のように issue が挙がっていた。

[Is it supposed to work on macOS High Sierra ? · Issue #13 · qmk/qmk_toolbox](https://github.com/qmk/qmk_toolbox/issues/13)

XCode のプロジェクトが公開されているのでビルドし直したら起動した。
しかし今度はライブラリが無いというエラーが発生して書き込みができない。

```sh
*** Attempting to flash, please don't remove device
>>> avrdude -p atmega32u4 -c avr109 -U flash:w:/Users/leico_studio/Downloads/qmk_firmware-master/vitamins_included_rev1_default.hex:i -P /dev/cu.usbmodem1451 -C avrdude.conf
    dyld: Library not loaded: /usr/local/lib/libftdi.1.dylib
      Referenced from: /Users/leico_studio/Downloads/qmk_toolbox-master/osx/DerivedData/QMK Toolbox/Build/Products/Debug/QMK Toolbox.app/Contents/Resources/avrdude
      Reason: image not found
```

## Homebrew の問題

{% capture text %}

Ran into this issue as well on High Sierra. 
As noted above `/usr/local/lib/libusb` was not found. 
Did a brew install (`brew install libusb`) but to no avail. 
The issue was that I had homebrew installed at `$HOME/homebrew`
rather than the standard `/usr/local`. 
Creating a new homebrew installation at `/usr/local`
and then running `brew install libusb` did the trick. 
Now the toolbox starts up as expected.

Perhaps the toolbox is assuming that `brew --prefix` will be `/usr/local`?

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [iansinnott (Ian Sinnott)](https://github.com/iansinnott)
from [Is it supposed to work on macOS High Sierra ? · Issue #13 · qmk/qmk_toolbox](https://github.com/qmk/qmk_toolbox/issues/13#issuecomment-361096893)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

Homebrew がデフォルトの `/usr/local` にインストールされていないとリンクエラーが起こるらしい。
なんじゃそりゃなので動的リンクなライブラリをバイナリに含める方法を調べる。


{% capture text %}


## ライブラリの本体パスと依存パスの設定

macのdylibはバイナリ内部に自身のパスと依存するライブラリ達のパスが書き込まれています。
この自身のパスは下記のようにして得られます。

```sh
$ otool -D libfoo.dylib
```

また、依存するライブラリのパスは下記のようにして得られます。

```sh
$ otool -L libfoo.dylib
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[macOSのコマンドラインアプリでdylibをよろしく扱う方法 - Qiita](https://qiita.com/omochimetaru/items/21d662b8df8bce1bc5ca)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

こんな機能があるらしい。では確認してみる。

```sh
o$ otool -L avrdude 
avrdude:
	@executable_path/../Frameworks/libusb-1.0.0.dylib (compatibility version 2.0.0, current version 2.0.0)
	@executable_path/../Frameworks/libusb-0.1.4.dylib (compatibility version 9.0.0, current version 9.4.0)
	/System/Library/Frameworks/CoreFoundation.framework/Versions/A/CoreFoundation (compatibility version 150.0.0, current version 1253.0.0)
	/System/Library/Frameworks/IOKit.framework/Versions/A/IOKit (compatibility version 1.0.0, current version 275.0.0)
	/usr/local/lib/libftdi.1.dylib (compatibility version 22.0.0, current version 22.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1225.1.1)
	/usr/lib/libedit.3.dylib (compatibility version 2.0.0, current version 3.0.0)
	/usr/lib/libncurses.5.4.dylib (compatibility version 5.4.0, current version 5.4.0)
```

_/usr/local/lib/libftdi.1.dylib_ というものがリンク切れしているようだ。
他のライブラリやバイナリなどにも同様にリンク切れをおこしているものがあった。

これを修正するには `install_name_tool` というものを使うらしい。

{% capture text %}

また、依存するライブラリのうち、一緒に配布するものは `@rpath` 直下を探すように書き換えます。
この例ではbarの中にfooへの依存があるのでこれを修正します。
コマンドの引数として元々のパスを指定しますが、これは先述の方法で取得した結果を用います。

```sh
$ install_name_tool -change "/Users/omochi/temp/libfoo.dylib" "@rpath/libfoo.dylib" libbar.dylib
```

ここで設定した `@rpath` は変数で、アプリが起動する時に変数展開されます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[macOSのコマンドラインアプリでdylibをよろしく扱う方法 - Qiita](https://qiita.com/omochimetaru/items/21d662b8df8bce1bc5ca)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

リンクの変更先だが、 _libusb-*_ の設定が既になされていて、それらは 

```sh
@executable_path/../Frameworks/libusb-1.0.0.dylib (compatibility version 2.0.0, current version 2.0.0)
@executable_path/../Frameworks/libusb-0.1.4.dylib (compatibility version 9.0.0, current version 9.4.0)
```

となっている。XCodeを見ると、 _@executable_path/../Frameworks/_ 内にコピーされるようになっている。


{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-qmk-toolbox/02_build_phase.png %}{% endcapture %}
{% capture caption %}

Build Phase の最後でライブラリをコピーしている。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`@exeutable_path` とは

{% capture text %}

そこで登場するのが `@executable_path` となります。
Mac OS X の dyld は、ライブラリのパスのなかに `@executable_path` があると
それを実行時のバイナリの場所に置き換える機能があります。
だから、 `Nantoka.app/Contents/Frameworks` に特定のバージョンのライブラリを置いて
それを使うってことができるんですね。 そこで、半恒久的に置き換える方法として次のような手段があります。

```sh
 $ install_name_tool -change \
    /System/Library/Frameworks/WebKit.framework/Versions/A/WebKit \
    @executable_path/../Frameworks/WebKit.framework/Versions/A/WebKit \
    /Applications/Line.app/Contents/MacOS/Line
```

`install_name_tool` を使うとバイナリが覚えてるライブラリのパスを書き換えることができます。
そこで、この例の場合は `/Applications/Line.app/Contents/Frameworks/`
に `WebKit.framework` を置けばそれを使ってくれます。
見つからない場合は `/System/Library/Frameworks` にフォールバックします。 
また、実際に `WebKit.framework` を置き換える場合は、
関連する `WebCore.framework` と `JavaScriptCore.framework` のそれぞれのパスも書き換えて、
セットで置き換える必要があります。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[アプリが使うFrameworkとかを置き換える方法 - blog.niw.at](http://blog.niw.at/post/25980282368)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

こういうことらしい。つまりAppのバイナリからの相対パスでライブラリを選択できると。
ひとまずこれで修正を行って実行をしてみた。

## アプリフリーズ、キーボード文鎮化

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-qmk-toolbox/03_freeze.png %}{% endcapture %}
{% capture caption %}

書き込み途中でフリーズした

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

しょうがないので強制終了を行った。案の定キーボードが動かなくなったので別途 AVR の ICSP ポートを用いて復帰をかけた記事がこちら。

[文鎮化した自作キーボードをArduinoを使って復帰させる]({{site.github.url}}{% link _docs/QMK/recover-avr.md %})

何が原因かよくわからないが、同様のエラーは `avrdude` のバイナリを使った際に起こったことがある。
なので homebrew からビルドしてコピーするのがよいのではないかと考えた。



## ビルド、コピー、リンク貼り直しを行うスクリプトを書く

コマンドやライブラリをビルドして集めてリンクを貼り直すスクリプトを書いた。

```sh
#!/bin/bash

# homebrew command check
if !(type brew --prefix > /dev/null 2>&1); then
  echo "homebrew not found" >&2
fi

homebrewdir=`brew --prefix`

# add fomulae ripository
brew tap leico/avr
brew tap PX4/homebrew-px4

# install requires
brew update
brew install avr-gcc@8_1
brew install dfu-programmer
brew install dfu-util
brew install gcc-arm-none-eabi
brew install avrdude
brew install teensy_loader_cli

# force copy requires on direcotry
  # binaries
cp -f $homebrewdir/bin/avrdude ./avrdude
cp -f $homebrewdir/bin/dfu-util ./dfu-util
cp -f $homebrewdir/bin/teensy_loader_cli ./teensy_loader_cli
cp -f $homebrewdir/bin/dfu-programmer ./dfu-programmer

  # libraries
cp -f $homebrewdir/lib/libusb-0.1.4.dylib ./libusb-0.1.4.dylib
cp -f $homebrewdir/lib/libusb-1.0.0.dylib ./libusb-1.0.0.dylib
cp -f $homebrewdir/lib/libftdi.1.dylib   ./libftdi.1.dylib

chmod 755 libusb-0.1.4.dylib libusb-1.0.0.dylib libftdi.1.dylib

# change link directory

  # get library directory
ftdidir=`otool -D libftdi.1.dylib | sed -n 2p`
usb_1dir=`otool -D libusb-1.0.0.dylib | sed -n 2p`
usb_0dir=`otool -D libusb-0.1.4.dylib | sed -n 2p`

  # avrdude
install_name_tool -change $ftdidir @executable_path/../Frameworks/libftdi.1.dylib avrdude 
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib avrdude 
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib avrdude 
  # dfu-util
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib dfu-util
  # libusb0.1.4
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib libusb-0.1.4.dylib
  # libftdi.1
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib libftdi.1.dylib

  # dfu-programmer
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib dfu-programmer
```

ここで公開している。
[qmk_toolbox/replace.sh at master · leico/qmk_toolbox](https://github.com/leico/qmk_toolbox/blob/master/osx/replace.sh)

のと、10.13で一応動くプロジェクトもここにあるが、 **書き込みミスが起きた場合キーボードが文鎮化するので自己責任で利用すること。
まだ実績が少ない。**
[leico/qmk_toolbox: A Toolbox companion for QMK Firmware](https://github.com/leico/qmk_toolbox)



### homebrew の存在をチェックする

{% capture text %}

## コマンドが入っていなかったらインストール

対象のコマンドが入っていなかったらインストールするときは、こう。

```sh
if !(type "コマンド" > /dev/null 2>&1); then
    # 対象のコマンドをインストールするような処理
fi
```

以上

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【シェルスクリプト】コマンドの有無による条件分岐 - Qiita](https://qiita.com/8ayac/items/b6b6f0a385d08659316b)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これでどうにかなるらしいがよくわからない。

{% capture text %}
## タイトルにあったコマンドの意味を考えてみる

```
command > /dev/null 2&>1
```

分けて考えましょう。

`2&>1`
: 標準エラー出力の結果を標準出力にマージする

`> /dev/null`
: 標準出力を捨てる

つまり、このコマンドの意味は、

**標準エラー出力の結果を標準出力にマージして、/dev/nullに捨てる**

という事だったわけです。
(普通標準エラー出力は捨てない方が良いです(cronで動かす場合))

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いい加減覚えよう。 `command > /dev/null 2>&1`の意味 - Qiita](https://qiita.com/ritukiii/items/b3d91e97b71ecd41d4ea)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`type Command` で `Command` が存在していたら標準出力からパスが返り、存在しない場合エラー出力からメッセージが出るらしい。
やってみる。

```sh
$ type cat 1>result
$ cat result
cat is hashed (/bin/cat)
```

コマンドがある場合は標準出力


```sh
$ type cot 2>result
$ cat result 
-bash: type: cot: not found
```

コマンドがない場合はエラーから出力されている。
つまり標準出力から何かが出力されない場合はコマンドが存在しないことになる。

{% capture text %}

## インストール先を取得するには

`brew --prefix` がインストール先を返してくれます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Homebrew のインストール先を変更する - Qiita](https://qiita.com/usamik26/items/601f5612bd3f8a21cc41)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これを利用する。さらに、バイナリやライブラリを集める際にも homebrew のディレクトリ位置が欲しいのでこの出力を変数に入れておく。

ここまでが

```sh
# homebrew command check
if !(type brew --prefix > /dev/null 2>&1); then
  echo "homebrew not found" >&2
fi

homebrewdir=`brew --prefix`
```

### homebrew で必要なものをビルドする


```sh
# add fomulae ripository
brew tap leico/avr
brew tap PX4/homebrew-px4

# install requires
brew update
brew install avr-gcc@8_1
brew install dfu-programmer
brew install dfu-util
brew install gcc-arm-none-eabi
brew install avrdude
brew install teensy_loader_cli
```

プロジェクトファイル内に存在しているものを列挙しただけのような感じ。
_libusb-*_ はこれらコマンドのインストール途中でインストールされる。

[文鎮化した自作キーボードをArduinoを使って復帰させる]({{site.github.url}}{% link _docs/QMK/recover-avr.md %})

に書いてあるとおり、gcc8.2.0 だと問題が起こるので
gcc8.1.0 用の formulae を用意してこちらをビルドしている。


### プロジェクトディレクトリへ強制コピーする

{% capture text %}

`cp` コマンドには「コピー先に同名のファイルがあった場合に強制上書きする」
というオプション `-f` があります。

```sh
# cp --help
:
:
-f, --force     if an existing destination file cannot be opened, remove it and try again.
:
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Linux の cp コマンドで強制上書き : まだプログラマーですが何か？](http://dotnsf.blog.jp/archives/2918928.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これで持ってくるが、 _HOMEBREW/bin_ や _HOMEBREW/lib_ の中身は基本シンボリックリンクである。
実体を持ってくることは可能なのだろうか。


{% capture text %}
`cp` コマンドでシンボリックリンクそのものをコピーしようとした場合、
名前を維持して実体化されてしまい
シンボリックリンクとしてコピーできない（これは普通だけどね…）。

```sh
$ ls /path/to/symlink
/path/to/symlink => substance
$ cp /path/to/symlink .
$ ls symlink
symlink # 中身は substance になる
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[cp コマンドでシンボリックをコピーする - Qiita](https://qiita.com/mattintosh4/items/b9a6e8a87c0cb9bbf00d)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


普通に実体がコピーされてくるらしいので問題なさそうだ。

ここまで

```sh
# force copy requires on direcotry
  # binaries
cp -f $homebrewdir/bin/avrdude ./avrdude
cp -f $homebrewdir/bin/dfu-util ./dfu-util
cp -f $homebrewdir/bin/teensy_loader_cli ./teensy_loader_cli
cp -f $homebrewdir/bin/dfu-programmer ./dfu-programmer

  # libraries
cp -f $homebrewdir/lib/libusb-0.1.4.dylib ./libusb-0.1.4.dylib
cp -f $homebrewdir/lib/libusb-1.0.0.dylib ./libusb-1.0.0.dylib
cp -f $homebrewdir/lib/libftdi.1.dylib   ./libftdi.1.dylib

```

### ライブラリの元々のフルパスを取得する

ここでライブラリが元々存在していたパスが必要になる。
`install_name_tool` での書き換えに元のパスが必要になるからだ。

{% capture text %}


## 2.2.使用ライブラリのディレクトリの変更

`otool -L` で表示されるライブラリが使用しているライブラリのディレクトリは、
`install_name_tool -change` で変更ができます。

```sh
install_name_tool -change [変更前のライブラリのフルパス名] [変更後のライブラリのフルパス名] [ライブラリ名]
```

---


変更方法

```sh
install_name_tool -change /opt/local/lib/libbz2.1.0.dylib @executable_path/../Frameworks/libbz2.1.0.dylib libboost_iostreams-mt.dylib
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Mac OS Xでライブラリの設定変更について - Qiita](https://qiita.com/itoru257/items/d36b4e3908a39bfbbb18)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

フルパスは以下のように取得できる。

{% capture text %}
## 1.1.ディレクトリの確認

ライブラリのディレクトリは、otool -D で確認できます。

```sh
otool -D [ライブラリ名]
```

(例)

```sh
$ otool -D libboost_iostreams-mt.dylib
libboost_iostreams-mt.dylib:
/opt/local/lib/libboost_iostreams-mt.dylib
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Mac OS Xでライブラリの設定変更について - Qiita](https://qiita.com/itoru257/items/d36b4e3908a39bfbbb18)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


出力の2行目がフルパスになるのでここだけを取得する。

{% capture text %}


## 10行目を表示する

```sh
sed -n 10p file
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ファイルの指定した行のみ表示する - Qiita](https://qiita.com/y_310/items/2c40e0671b89ce6c15d5)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ここまで

```sh
chmod 755 libusb-0.1.4.dylib libusb-1.0.0.dylib libftdi.1.dylib

# change link directory

  # get library directory
ftdidir=`otool -D libftdi.1.dylib | sed -n 2p`
usb_1dir=`otool -D libusb-1.0.0.dylib | sed -n 2p`
usb_0dir=`otool -D libusb-0.1.4.dylib | sed -n 2p`
```

パーミッションが違って書き換えられない時があったので `chmod` を挟んでいる。



### ライブラリの参照先を変更する

あとは `install_name_tool` で参照先を変更するだけ

```sh
  # avrdude
install_name_tool -change $ftdidir @executable_path/../Frameworks/libftdi.1.dylib avrdude 
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib avrdude 
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib avrdude 
  # dfu-util
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib dfu-util
  # libusb0.1.4
install_name_tool -change $usb_1dir @executable_path/../Frameworks/libusb-1.0.0.dylib libusb-0.1.4.dylib
  # libftdi.1
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib libftdi.1.dylib

  # dfu-programmer
install_name_tool -change $usb_0dir @executable_path/../Frameworks/libusb-0.1.4.dylib dfu-programmer
```

### XCode の設定を変更する

_libftdi.1.dylib_ が現状埋め込まれない状態になっている。ライセンスの確認をした上で埋め込むことにした。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/build-qmk-toolbox/04_embeded.png %}{% endcapture %}
{% capture caption %}

Embeded Binaries に設定が終わった _libftdi.1.dylib_ をドラッグ&ドロップする。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



## 動作確認

実際に動作させて確認すると、

* **QMK Tools でダウンロードされたデフォルトのファームウェアは書き込みに失敗する**
    * 案の定USB接続が死んで ICSP ポートから復帰させることになる
* 自分のコンピュータで qmk-firmware でビルドしたデフォルトのファームなら書き込み完了する


ということが判明した。ひとまずここまでで諦める。
