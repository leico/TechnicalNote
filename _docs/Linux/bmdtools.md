---
layout : post
title  : LinuxプログラムでBlackmagic Design製品を扱う
date : 2017/10/18
lastchange : 2017-10-20 00:19:25.
tags   :
  - blackmagic design
  - Ultrastudio Mini Recorder
  - bmdtools
  - ffmepg
  - avconv
---

## ただ録画する以外にも使えるはずだ

高品質に動画キャプチャができるのだから、インタラクティブなシステムや配信などに利用したい。
Blackmagicが公式でSDKを配布しており、マニュアルを見る限り大抵のことはSDK経由で操作可能なように見受けられる。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/00_sdk_manual.png %}{% endcapture %}
{% assign caption = '
配布されているSDKの内部にドキュメントが付属している。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

が、機能が多すぎて読解と実装で膨大な時間を消費する。
しかし映像の取り込みや送出制御程度であれば[lu-zero](https://github.com/lu-zero)氏によって公開されていてる
[bmdtools](https://github.com/lu-zero/bmdtools)が利用できそうだ。

今回は bmdtools を利用して録画してみる。


参考:

* [lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools)
    * [Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
* [Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)

* [NASM](http://www.nasm.us/)
    * [NASM - The Netwide Assembler](http://www.nasm.us/xdoc/2.13.01/html/nasmdoc1.html)
* [猫科研究所 - 今更MinGW 2009.06\(5\) nasm,yasm](http://up-cat.net/%25BA%25A3%25B9%25B9MinGW%2B2009%252E06%25285%2529%2Bnasm%252Cyasm.html)
* [RPM を使わずに make install した場合のアンインストール方法](https://futuremix.org/2009/01/configure-make-uninstall)
* [GNU make](https://www.gnu.org/software/make/manual/make.html#Remaking-Makefiles)

* [x264, the best H.264/AVC encoder - VideoLAN](https://www.videolan.org/developers/x264.html)
* [Detailed Build Instructions missing · Issue #52 · lu-zero/bmdtools](https://github.com/lu-zero/bmdtools/issues/52#issuecomment-335691120)

* [The Yasm Modular Assembler Project](http://yasm.tortall.net/)

* [x265/README.txt at master · videolan/x265](https://github.com/videolan/x265/blob/master/build/README.txt)
* [linux - CMake complains "The CXX compiler identification is unknown" - Stack Overflow](https://stackoverflow.com/a/9702678)

* [opencore-amr download \| SourceForge.net](https://sourceforge.net/projects/opencore-amr/)
    * [opencore-amr -  Browse /fdk-aac at SourceForge.net](https://sourceforge.net/projects/opencore-amr/files/fdk-aac/)

* [Opus Codec](http://opus-codec.org/)

* [alsa - FFmpeg doesn't recognize my audio sources - Unix & Linux Stack Exchange](https://unix.stackexchange.com/a/141201)
* [audio - FFMPEG: Unknown input format: 'alsa' - Raspberry Pi Stack Exchange](https://raspberrypi.stackexchange.com/a/70544)

* [ジョブ実行時に error while loading shared libraries が出力されて失敗する場合の対応方法 \| TSUBAME計算サービス](http://tsubame.gsic.titech.ac.jp/node/1340)
* [Linux で error while loading shared libraries: が表示されて実行できない場合 \[ へっぽこSEのメモ帳 \]](http://pucyan.blog77.fc2.com/blog-entry-73.html)
* [linux - How to do runtime linking in make using LDFLAGS -R option, or some other way - Stack Overflow](https://stackoverflow.com/a/17255045)
* [変な場所にインストールした共有ライブラリを使ってもらえないとき対策(RPATH) - Qiita](https://qiita.com/woremacx/items/41bfd22f35f0f3b3c143)
* [configureするときのLDFLAGSやLIBSとは - QA@IT](https://qa.atmarkit.co.jp/q/334)

* [/usr/local/libの共有ライブラリ(.so)を参照しないとき - 計算機と戯れる日々](http://d.hatena.ne.jp/n9d/20080820/1219228809)


## bmdtools をコンパイルする



{% capture text %}
## Build instructions

In order to build it just clone/unpack this on your Sample directory from the DeckLink SDK and then issue "make".
If you have [Libav](https://libav.org/) and [pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)
or [pkgconf](https://github.com/pkgconf/pkgconf) installed it will build fine.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





bmdtools はソースコードが落ちているだけなので、必要なライブラリを揃えて自分でコンパイルしなければならない。
必要なライブラリ等は bmdtools の[README](https://github.com/lu-zero/bmdtools) や
[Wiki](https://github.com/lu-zero/bmdtools/wiki) などにまとめられている。

実際ほぼ Wiki 内の[Building from source](https://github.com/lu-zero/bmdtools/wiki#building-from-source) に沿う形で実行する。



### Blackmagic Desktop Video SDK のダウンロードと解凍

bmdtools のコンパイルに欠かせない Blackmagic 公式の SDK を
[Blackmagic Design: サポートセンター](https://www.blackmagicdesign.com/jp/support/)
からダウンロードする。



{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/01_download_sdk.png %}{% endcapture %}
{% capture caption %}
**capture and playback** 製品を選択すると、左カラムの中に用意されている。

{{ page.lastchange }} 現在、10.9.5
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/02_resistration.png %}{% endcapture %}
{% capture caption %}
ダウンロードには登録と利用規約に同意する必要がある。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/03_agreement.png %}{% endcapture %}
{% capture caption %}
同意するボタンの色が薄くなっていて選択できないような印象を受けるが、ちゃんとクリックできる。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


ダウンロード完了したらSDKを解凍する。

```sh
$ unzip Blackmagic_DeckLink_SDK_10.9.5.zip
```

### コンパイル環境の構築


{% capture text %}
#### Prerequisites

The system should provide at least `pkgconf` or `pkg-config`, a `C99` compiler, `gnu make`, 
`autotools` (in order to build `opus` and `fdk-aac`) and `cmake` (to build `x265`).
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




コンパイルに必要なソフトウェアを準備する。

```sh
sudo apt-get install ...
```

で適宜パッケージからインストール、あるいは必要に応じてソースからコンパイル、インストールを行う。




### nasmのコンパイルとインストール


{% capture text %}
nasmとyasmはどちらもx86の **SIMD命令（MMX,SSE,SSE2等）に対応したアセンブラ** で、
殆どのケースでこれらの命令を利用した高速化のために使用される。
一昔前にはnasmがこの用途での一人勝ち状態であったが、一時期更新が滞り、
新規命令に対応しなかったことから、リプレースとしてyasmが登場した。その後nasmの更新は再開したが、
yasmに移行してしまったプロジェクトも多いため、**用途は同じソフトウェアではあるが、両方インストールしておくのが間違いない。**
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[猫科研究所 - 今更MinGW 2009.06\(5\) nasm,yasm](http://up-cat.net/%25BA%25A3%25B9%25B9MinGW%2B2009%252E06%25285%2529%2Bnasm%252Cyasm.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}
### 1.1 What Is NASM?

The Netwide Assembler, NASM, is an 80x86 and x86-64 assembler designed for portability and modularity.
It supports a range of object file formats, including Linux and \*BSD a.out, ELF, COFF, Mach-O, 
16-bit and 32-bit OBJ (OMF) format, Win32 and Win64. 
It will also output plain binary files, Intel hex and Motorola S-Record formats. 
Its syntax is designed to be simple and easy to understand, 
similar to the syntax in the Intel Software Developer Manual with minimal complexity. 
It supports all currently known x86 architectural extensions, and has strong support for macros.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[NASM - The Netwide Assembler](http://www.nasm.us/xdoc/2.13.01/html/nasmdoc1.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




NASMは最新の命令セットに対応したアセンブラ言語、X86だけではなく、X64にも対応しているということらしい。



{% capture text %}
##### nasm

nasm is needed to build `x264` and `libav`.

```sh
# git clone git://repo.or.cz/nasm.git
# cd nasm
# autoreconf -ivf
# ./configure
# make
# make install
```

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

h264やその他コーデックの変換、ビデオストリームの編集等にNASMで高速化したモジュールを利用しているということか。

Wikiではgitのコードからコンパイルを行っているが、その場合上記の様に`autoreconf` で `configure` を生成する必要がある。
この `configure` 生成のタイミングでコケてしまうと面倒なので、今回のビルドでは全て最新の stable 版をダウンロードしてビルドする。
その場合 `configure` も生成された状態でアップロードされているので`autoreconf`する必要もない。


[Index of /pub/nasm/stable](http://www.nasm.us/pub/nasm/stable/)より、最新版のNASMをダウンロードする。




{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/04_download_nasm.png %}{% endcapture %}
{% capture caption %}
**xdoc** と付いているものはマニュアルなので注意。

{{ page.lastchange }} 現在、2.13.01
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



ダウンロードしたものを解凍し、`configure`を行う。

```sh
$ tar xjvf nasm-2.13.01.tar.bz2
$ cd nasm-2.13.01/
$ ./configure
```

`configure` が成功したら`make`でビルド、後に`make install`でインストールする。

```sh
$ make
$ sudo make install
```

アンインストールするには






{% capture text %}
 一応、Makefile が残っていれば、

```sh
# make uninstall
```

でアンインストールできるものもあります。
autoconf, automake で作成された MakeFile であれば、大抵は対応しています。 
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[RPM を使わずに make install した場合のアンインストール方法](https://futuremix.org/2009/01/configure-make-uninstall)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





```sh
$ sudo make uninstall
```

で削除できる。また、







{% capture text %}
もしくは、インストール時のログを保存しておき、それを参考に削除するという方法もあります。
make 後に install する前に

```sh
$ make -n install
```

とすれば、実際のインストールは行なわずにファイルがどこにインストールされるかを表示してくれます。
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[RPM を使わずに make install した場合のアンインストール方法](https://futuremix.org/2009/01/configure-make-uninstall)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
-n
\--just-print
\--dry-run
\--recon
: No-op. Causes make to print the recipes that are needed to make the targets up to date, 
but not actually execute them. Note that some recipes are still executed, 
even with this flag
(see [How the MAKE Variable Works](https://www.gnu.org/software/make/manual/make.html#MAKE-Variable)).
Also any recipes needed to update included makefiles are still executed
(see [How Makefiles Are Remade](https://www.gnu.org/software/make/manual/make.html#Remaking-Makefiles)).
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[GNU make](https://www.gnu.org/software/make/manual/make.html#Remaking-Makefiles)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





`-n`オプションで実際に実行されるコマンドが表示されるため、
これを確認してインストールされるファイルを削除することも一応可能。

```sh
$ make install -n
mkdir -p /usr/local/bin
/usr/bin/install -c nasm /usr/local/bin/nasm
/usr/bin/install -c ndisasm /usr/local/bin/ndisasm
mkdir -p /usr/local/share/man/man1
/usr/bin/install -c -m 644 ./nasm.1 /usr/local/share/man/man1/nasm.1
/usr/bin/install -c -m 644 ./ndisasm.1 /usr/local/share/man/man1/ndisasm.1
```



### libx264のコンパイルとインストール

{% capture text %}
##### x264

```sh
# git clone git://github.com/videolan/x264.git
# cd x264
# ./configure --enable-static --disable-lavf
# make
# make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



こちらも stable 版を[x264, the best H.264/AVC encoder - VideoLAN](https://www.videolan.org/developers/x264.html)からダウンロードする。

この部分、





{% capture text %}
Hi @lu-zero Thanks to publish bmdtools.
I note that missed x264 "library".

> ```sh
> # git clone git://github.com/videolan/x264.git
> # cd x264
> # ./configure
> # make
> # make install
> ```

This way generate x264 binary only.

> ```sh
> # ./configure --enable-static --enable-shared
> # make
> # make install
> ```
> 
> ---
>
> * [CompilationGuide/Quick/libx264 – FFmpeg](https://trac.ffmpeg.org/wiki/CompilationGuide/Quick/libx264)

It generates libx264 and fix libx264 not found when `libav configure enabled libx264` flag.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Detailed Build Instructions missing · Issue #52 · lu-zero/bmdtools](https://github.com/lu-zero/bmdtools/issues/52#issuecomment-335691120)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

という指摘をして最近変更されている。`--disable-lavf`の必要があるか不明だがとりあえず解凍して`configure`を走らせてみる。

```sh
$ tar xjvf last_x264.tar.bz2
$ cd x264-snapshot-20171018-2245
$ ./configure --enable-static --enable-shared
platform:      X86_64
byte order:    little-endian
system:        LINUX
cli:           yes
libx264:       internal
shared:        yes
static:        yes
asm:           yes
interlaced:    yes
avs:           avxsynth
lavf:          no
ffms:          no
mp4:           no
gpl:           yes
thread:        posix
opencl:        yes
filters:       crop select_every 
lto:           no
debug:         no
gprof:         no
strip:         no
PIC:           yes
bit depth:     8
chroma format: all

You can run 'make' or 'make fprofiled' now.
```


`configure`のログを見る限り、`lavf:          no`なので、`--disable-lavf`フラグは今の環境ではいらないようだ。
`make`でコンパイル、`make install`でインストールする。


```sh
$ make
$ sudo make install
```

### yasmのコンパイル及びインストール

x265で yasm アッセンブリによる高速化の恩恵にあやかるため、yasm をインストールする。
[Download - The Yasm Modular Assembler Project](http://yasm.tortall.net/Download.html) から Latest Release のソースコードをダウンロードして解凍。

```sh
$ tar xvf yasm-1.3.0.tar.gz
```

`configure`、`make`、`make install`

```sh
$ ./configure
$ make
$ sudo make install
make  install-recursive
make[1]: Entering directory '/home/test/Downloads/yasm-1.3.0'
Making install in po
make[2]: Entering directory '/home/test/Downloads/yasm-1.3.0/po'
if test "yasm" = "gettext-tools"; then \
  /bin/mkdir -p /usr/local/share/gettext/po; \
  for file in Makefile.in.in remove-potcdate.sin quot.sed boldquot.sed en@quot.header en@boldquot.header insert-header.sin Rules-quot   Makevars.template; do \
    /usr/bin/install -c -m 644 ./$file \
		    /usr/local/share/gettext/po/$file; \
  done; \
  for file in Makevars; do \
    rm -f /usr/local/share/gettext/po/$file; \
  done; \
else \
  : ; \
fi
make[2]: Leaving directory '/home/test/Downloads/yasm-1.3.0/po'
Making install in .
make[2]: Entering directory '/home/test/Downloads/yasm-1.3.0'
make[3]: Entering directory '/home/test/Downloads/yasm-1.3.0'
 /bin/mkdir -p '/usr/local/bin'
  /usr/bin/install -c yasm ytasm vsyasm '/usr/local/bin'
 /bin/mkdir -p '/usr/local/lib'
 /usr/bin/install -c -m 644  libyasm.a '/usr/local/lib'
 ( cd '/usr/local/lib' && ranlib libyasm.a )
make  install-exec-hook
make[4]: Entering directory '/home/test/Downloads/yasm-1.3.0'
make[4]: Nothing to be done for 'install-exec-hook'.
make[4]: Leaving directory '/home/test/Downloads/yasm-1.3.0'
 /bin/mkdir -p '/usr/local/include'
 /usr/bin/install -c -m 644 libyasm.h '/usr/local/include'
 /bin/mkdir -p '/usr/local/share/man/man1'
 /bin/mkdir -p '/usr/local/share/man/man1'
 /usr/bin/install -c -m 644 yasm.1 '/usr/local/share/man/man1'
 /bin/mkdir -p '/usr/local/share/man/man7'
 /usr/bin/install -c -m 644 yasm_arch.7 yasm_parsers.7 yasm_dbgfmts.7 yasm_objfmts.7 '/usr/local/share/man/man7'
 /bin/mkdir -p '/usr/local/share/man/man7'
 /bin/mkdir -p '/usr/local/include/libyasm'
 /usr/bin/install -c -m 644 libyasm/arch.h libyasm/assocdat.h libyasm/bitvect.h libyasm/bytecode.h libyasm/compat-queue.h libyasm/coretype.h libyasm/dbgfmt.h libyasm/errwarn.h libyasm/expr.h libyasm/file.h libyasm/floatnum.h libyasm/hamt.h libyasm/insn.h libyasm/intnum.h libyasm/inttree.h libyasm/linemap.h libyasm/listfmt.h libyasm/md5.h libyasm/module.h libyasm/objfmt.h libyasm/parser.h libyasm/phash.h libyasm/preproc.h libyasm/section.h libyasm/symrec.h libyasm/valparam.h libyasm/value.h '/usr/local/include/libyasm'
 /bin/mkdir -p '/usr/local/include'
 /usr/bin/install -c -m 644 libyasm-stdint.h '/usr/local/include'
make[3]: Leaving directory '/home/test/Downloads/yasm-1.3.0'
make[2]: Leaving directory '/home/test/Downloads/yasm-1.3.0'
make[1]: Leaving directory '/home/test/Downloads/yasm-1.3.0'
```


### x265のコンパイル及びインストール


{% capture text %}
##### x265

```sh
# git clone git://github.com/videolan/x265
# cd x265/build
# cmake ../source
# make
# make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


stable版を[Releases · videolan/x265](https://github.com/videolan/x265/releases)からダウンロードして解凍。

```sh
tar xzvf x265-2.5.tar.gz
```


{% capture text %}
Or use our shell script which runs cmake then opens the curses GUI to
configure build options

1. cd build/linux ; ./make-Makefiles.bash
2. make
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[x265/README.txt at master · videolan/x265](https://github.com/videolan/x265/blob/master/build/README.txt)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

こう書いてあったので _README.txt_ の方法でビルドすることにした。
ここで`ccmake`が必要になる。







`ccmake`をインストール

```sh
$sudo apt-get install cmake-curses-gui
```



_build/linux_ に移動して `./make-Makefiles.bash` したら怒られた。

```sh
$ ./make-Makefiles.bash 
-- cmake version 3.5.1
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is unknown
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
CMake Error at CMakeLists.txt:19 (project):
  No CMAKE_CXX_COMPILER could be found.

  Tell CMake where to find the compiler by setting either the environment
  variable "CXX" or the CMake cache entry CMAKE_CXX_COMPILER to the full path
  to the compiler, or to the compiler name if it is in the PATH.


-- Configuring incomplete, errors occurred!
See also "/home/test/Downloads/x265-2.5/build/linux/CMakeFiles/CMakeOutput.log".
See also "/home/test/Downloads/x265-2.5/build/linux/CMakeFiles/CMakeError.log".
```


{% capture text %}
Your `/home/gnu/bin/c++` seem to require additional flag to link things properly and CMake doesn't know about that.
To use `/usr/bin/c++` as your compiler run `cmake` with `-DCMAKE_CXX_COMPILER=/usr/bin/c++`.

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
by [arrowd](https://stackoverflow.com/users/637669/arrowd) / [linux - CMake complains "The CXX compiler identification is unknown" - Stack Overflow](https://stackoverflow.com/a/9702678)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`-DCMAKE_CXX_COMPILER=`を実行時に指定すれば動く。が、c++が存在しない。

```sh
$ whereis c++
c++:
```

のでC++コンパイラとして`g++`をインストールする。

```sh
sudo apt-get install g++
```

改めて、

```sh
$ ./make-Makefiles.bash 
-- cmake version 3.5.1
-- The CXX compiler identification is GNU 5.4.0
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Detected x86_64 target processor
-- Could NOT find NUMA (missing:  NUMA_ROOT_DIR NUMA_INCLUDE_DIR NUMA_LIBRARY) 
-- Looking for include file inttypes.h
-- Looking for include file inttypes.h - found
-- Performing Test CC_HAS_NO_STRICT_OVERFLOW
-- Performing Test CC_HAS_NO_STRICT_OVERFLOW - Success
-- Performing Test CC_HAS_NO_NARROWING
-- Performing Test CC_HAS_NO_NARROWING - Success
-- Performing Test CC_HAS_NO_ARRAY_BOUNDS
-- Performing Test CC_HAS_NO_ARRAY_BOUNDS - Success
-- Performing Test CC_HAS_FAST_MATH
-- Performing Test CC_HAS_FAST_MATH - Success
-- Performing Test CC_HAS_STACK_REALIGN
-- Performing Test CC_HAS_STACK_REALIGN - Success
-- Performing Test CC_HAS_FNO_EXCEPTIONS_FLAG
-- Performing Test CC_HAS_FNO_EXCEPTIONS_FLAG - Success
-- Found yasm: /usr/local/bin/yasm (found version "1.3.0") 
-- Found Yasm 1.3.0 to build assembly primitives
-- x265 version 0.0
-- The ASM_YASM compiler identification is unknown
-- Found assembler: /usr/local/bin/yasm
-- Looking for strtok_r
-- Looking for strtok_r - found
-- Looking for include file getopt.h
-- Looking for include file getopt.h - found
-- Configuring done
-- Generating done
-- Build files have been written to: /home/test/Downloads/x265-2.5/build/linux
```




`make`、`make install`

```sh
$ make
$ sudo make install
[ 61%] Built target common
[ 85%] Built target encoder
[ 86%] Built target x265-shared
[ 87%] Built target x265-static
[100%] Built target cli
Install the project...
-- Install configuration: "Release"
-- Installing: /usr/local/lib/libx265.a
-- Installing: /usr/local/include/x265.h
-- Installing: /usr/local/include/x265_config.h
-- Installing: /usr/local/lib/libx265.so.130
-- Installing: /usr/local/lib/libx265.so
-- Installing: /usr/local/lib/pkgconfig/x265.pc
-- Installing: /usr/local/bin/x265
-- Set runtime path of "/usr/local/bin/x265" to ""
```



### fdk-aacのコンパイル、インストール




{% capture text %}
##### fdk-aac

```sh
# git clone git://git.code.sf.net/p/opencore-amr/fdk-aac
# cd fdk-aac
# autoreconf -if
# ./configure
# make
# make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




[opencore-amr -  Browse /fdk-aac at SourceForge.net](https://sourceforge.net/projects/opencore-amr/files/fdk-aac/)
からダウンロードして解凍する。





```sh
$ tar xzvf fdk-aac-0.1.5.tar.gz 
```

`configure`、`make`、`make install`

```sh
$ ./configure
$ make
$ sudo make install
[sudo] password for test: 
make[1]: Entering directory '/home/test/Downloads/fdk-aac-0.1.5'
 /bin/mkdir -p '/usr/local/lib'
 /bin/bash ./libtool   --mode=install /usr/bin/install -c   libfdk-aac.la '/usr/local/lib'
libtool: install: /usr/bin/install -c .libs/libfdk-aac.so.1.0.0 /usr/local/lib/libfdk-aac.so.1.0.0
libtool: install: (cd /usr/local/lib && { ln -s -f libfdk-aac.so.1.0.0 libfdk-aac.so.1 || { rm -f libfdk-aac.so.1 && ln -s libfdk-aac.so.1.0.0 libfdk-aac.so.1; }; })
libtool: install: (cd /usr/local/lib && { ln -s -f libfdk-aac.so.1.0.0 libfdk-aac.so || { rm -f libfdk-aac.so && ln -s libfdk-aac.so.1.0.0 libfdk-aac.so; }; })
libtool: install: /usr/bin/install -c .libs/libfdk-aac.lai /usr/local/lib/libfdk-aac.la
libtool: install: /usr/bin/install -c .libs/libfdk-aac.a /usr/local/lib/libfdk-aac.a
libtool: install: chmod 644 /usr/local/lib/libfdk-aac.a
libtool: install: ranlib /usr/local/lib/libfdk-aac.a
libtool: finish: PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/sbin" ldconfig -n /usr/local/lib
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------
 /bin/mkdir -p '/usr/local/include/fdk-aac'
 /usr/bin/install -c -m 644 ./libSYS/include/machine_type.h ./libSYS/include/genericStds.h ./libSYS/include/FDK_audio.h ./libAACenc/include/aacenc_lib.h ./libAACdec/include/aacdecoder_lib.h '/usr/local/include/fdk-aac'
 /bin/mkdir -p '/usr/local/lib/pkgconfig'
 /usr/bin/install -c -m 644 fdk-aac.pc '/usr/local/lib/pkgconfig'
make[1]: Leaving directory '/home/test/Downloads/fdk-aac-0.1.5'
```

### opus codecのコンパイル、インストール




{% capture text %}
##### opus

```sh
# git clone git://git.opus-codec.org/opus.git
# cd opus
# autoreconf -if
# ./configure
# make
# make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




[Downloads – Opus Codec](http://opus-codec.org/downloads/)から stable release のソースコードをダウンロード、解凍する。

```sh
 $ tar xzvf opus-1.2.1.tar.gz
```

`configure`、`make`、`make install`

```sh
$ ./configure
$ make
$ make install
make  install-recursive
make[1]: Entering directory '/home/test/Downloads/opus-1.2.1'
make[2]: Entering directory '/home/test/Downloads/opus-1.2.1'
make[3]: Entering directory '/home/test/Downloads/opus-1.2.1/doc'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/home/test/Downloads/opus-1.2.1/doc'
make[3]: Entering directory '/home/test/Downloads/opus-1.2.1'
 /bin/mkdir -p '/usr/local/lib'
 /bin/bash ./libtool   --mode=install /usr/bin/install -c   libopus.la '/usr/local/lib'
libtool: install: /usr/bin/install -c .libs/libopus.so.0.6.1 /usr/local/lib/libopus.so.0.6.1
libtool: install: (cd /usr/local/lib && { ln -s -f libopus.so.0.6.1 libopus.so.0 || { rm -f libopus.so.0 && ln -s libopus.so.0.6.1 libopus.so.0; }; })
libtool: install: (cd /usr/local/lib && { ln -s -f libopus.so.0.6.1 libopus.so || { rm -f libopus.so && ln -s libopus.so.0.6.1 libopus.so; }; })
libtool: install: /usr/bin/install -c .libs/libopus.lai /usr/local/lib/libopus.la
libtool: install: /usr/bin/install -c .libs/libopus.a /usr/local/lib/libopus.a
libtool: install: chmod 644 /usr/local/lib/libopus.a
libtool: install: ranlib /usr/local/lib/libopus.a
libtool: finish: PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/sbin" ldconfig -n /usr/local/lib
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------
make[4]: Entering directory '/home/test/Downloads/opus-1.2.1/doc'
make[5]: Entering directory '/home/test/Downloads/opus-1.2.1/doc'
make[5]: Nothing to be done for 'install-exec-am'.
make[5]: Nothing to be done for 'install-data-am'.
make[5]: Leaving directory '/home/test/Downloads/opus-1.2.1/doc'
make[4]: Leaving directory '/home/test/Downloads/opus-1.2.1/doc'
 /bin/mkdir -p '/usr/local/share/aclocal'
 /usr/bin/install -c -m 644 opus.m4 '/usr/local/share/aclocal'
 /bin/mkdir -p '/usr/local/lib/pkgconfig'
 /usr/bin/install -c -m 644 opus.pc '/usr/local/lib/pkgconfig'
 /bin/mkdir -p '/usr/local/include/opus'
 /usr/bin/install -c -m 644 include/opus.h include/opus_multistream.h include/opus_types.h include/opus_defines.h '/usr/local/include/opus'
make[3]: Leaving directory '/home/test/Downloads/opus-1.2.1'
make[2]: Leaving directory '/home/test/Downloads/opus-1.2.1'
make[1]: Leaving directory '/home/test/Downloads/opus-1.2.1'
```





### libavのコンパイル、インストール






{% capture text %}
##### libav

```sh
# git clone git://github.com/libav/libav
# cd libav
# ./configure --enable-gpl --enable-nonfree --enable-libx264 --enable-libx265 --enable-libfdk-aac
# make -j 8 && make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






[Libav](https://libav.org/download/)から最新版をダウンロード、解凍。

```sh
$ tar xzvf libav-12.2.tar.gz
```

`configure`

```sh
$ ./configure --enable-gpl --enable-nonfree --enable-libx264 --enable-libx265 --enable-libfdk-aac
...
Enabled indevs:
dv1394                    fbdev                     oss                      
v4l2                                                                         

Enabled outdevs:
oss                                                                          
...
```

デバイス覧にALSAがいない。音声の入出力ができないのでどうにかする。




{% capture text %}
ALSA support isn't inherently built into `ffmpeg`. 
You need to have the ALSA development files installed at `./configure` time when building `ffmpeg`.

The `ffmpeg` configure script looks for `alsa/asoundlib.h` and `libasound`.
If either is missing, it simply won't build ALSA support into the program.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Warren Young](https://unix.stackexchange.com/users/138/warren-young) / 
[alsa - FFmpeg doesn't recognize my audio sources - Unix & Linux Stack Exchange](https://unix.stackexchange.com/a/141201)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






{% capture text %}
# Short answer

run:

```sh
sudo apt-get install libasound2-dev
```

before attempting to configure and compile ffmpeg
[or anything](https://raspberrypi.stackexchange.com/questions/15018/sound-recording-program-using-nano#15021)
where you need alsa support.



# Process for resolving similar build dependencies:

Returning to this build/install method:

```sh
$ cd /usr/src
$ sudo git clone git://source.ffmpeg.org/ffmpeg.git
$ cd ffmpeg/
$ sudo ./configure && sudo make && sudo make install
```

the last line is a compaction of three steps, 
and the first one is significant as `configure` establishes 
what optional features the binary will support.

It is normal to change system libraries and configuration options 
and rerun `configure` many times until you are happy with its results.

The output you want is "alsa" as a supported indev (and probably outdev.)

```sh
./configure
  ... no alsa .. 
./configure --help
./configure --list-indevs
./configure --enable-indev=alsa --enable-outdev=alsa
  ... no alsa .. so it is not just off by default..
grep alsa configure
      alsa_asoundlib_h
      alsa
  alsa_indev_deps="alsa"
  alsa_outdev_deps="alsa"
  enabled_any alsa_indev alsa_outdev &&
      check_lib alsa alsa/asoundlib.h snd_pcm_htimestamp -lasound
```

this leads to why configure is not supporting alsa, and looking up asoundlib.h goes 
[here](https://raspberrypi.stackexchange.com/questions/15018/sound-recording-program-using-nano#15021)
though you could also query the packaging db.

```sh
sudo apt-get install libasound2-dev
./configure
 ... alsa! ..
./make
./ffmpeg -formats |grep alsa
 ...  DE alsa! .. (the D/E columns indicate both directions are working)
./make install
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [lossleader](https://raspberrypi.stackexchange.com/users/10699/lossleader) /
[audio - FFMPEG: Unknown input format: 'alsa' - Raspberry Pi Stack Exchange](https://raspberrypi.stackexchange.com/a/70544)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





_asoundlib.h_ がないのが原因らしい。そして _asoundlib.h_ は _libasound2-dev_ に入っているようだ。

_libasound2-dev_ を探してインストールする。



```sh
$ apt-cache search libasound
libasound2-dev - shared library for ALSA applications -- development files
```

```sh
sudo apt-get install libasound2-dev
```

```sh
$ ./configure --enable-gpl --enable-nonfree --enable-libx264 --enable-libx265 --enable-libfdk-aac
...
Enabled indevs:
alsa                      dv1394                    fbdev                    
oss                       v4l2                                               

Enabled outdevs:
alsa                      oss                                                
```




ALSAが見えた。`make`、`make install`

```sh
$ make
$ sudo make install
INSTALL	doc/avconv.1
INSTALL	doc/avprobe.1
INSTALL	install-progs-yes
INSTALL	avconv
INSTALL	avprobe
INSTALL	presets/libx264-lossless_slower.avpreset
INSTALL	presets/libx264-faster.avpreset
INSTALL	presets/libx264-lossless_max.avpreset
INSTALL	presets/libvpx-1080p.avpreset
INSTALL	presets/libx264-medium_firstpass.avpreset
INSTALL	presets/libx264-baseline.avpreset
INSTALL	presets/libvpx-720p50_60.avpreset
INSTALL	presets/libx264-lossless_ultrafast.avpreset
INSTALL	presets/libx264-placebo_firstpass.avpreset
INSTALL	presets/libx264-lossless_fast.avpreset
INSTALL	presets/libvpx-360p.avpreset
INSTALL	presets/libx264-veryfast.avpreset
INSTALL	presets/libx264-medium.avpreset
INSTALL	presets/libx264-veryslow.avpreset
INSTALL	presets/libx264-veryslow_firstpass.avpreset
INSTALL	presets/libx264-placebo.avpreset
INSTALL	presets/libx264-slow_firstpass.avpreset
INSTALL	presets/libx264-ipod320.avpreset
INSTALL	presets/libx264-slower_firstpass.avpreset
INSTALL	presets/libvpx-720p.avpreset
INSTALL	presets/libx264-ultrafast_firstpass.avpreset
INSTALL	presets/libx264-ipod640.avpreset
INSTALL	presets/libx264-faster_firstpass.avpreset
INSTALL	presets/libx264-slow.avpreset
INSTALL	presets/libx264-superfast_firstpass.avpreset
INSTALL	presets/libx264-lossless_medium.avpreset
INSTALL	presets/libx264-slower.avpreset
INSTALL	presets/libx264-main.avpreset
INSTALL	presets/libx264-ultrafast.avpreset
INSTALL	presets/libx264-superfast.avpreset
INSTALL	presets/libvpx-1080p50_60.avpreset
INSTALL	presets/libx264-veryfast_firstpass.avpreset
INSTALL	presets/libx264-lossless_slow.avpreset
INSTALL	presets/libx264-fast.avpreset
INSTALL	presets/libx264-fast_firstpass.avpreset
INSTALL	libavdevice/libavdevice.a
INSTALL	libavfilter/libavfilter.a
INSTALL	libavformat/libavformat.a
INSTALL	libavcodec/libavcodec.a
INSTALL	libavresample/libavresample.a
INSTALL	libswscale/libswscale.a
INSTALL	libavutil/libavutil.a
INSTALL	libavdevice/avdevice.h
INSTALL	libavdevice/version.h
INSTALL	libavdevice/libavdevice.pc
INSTALL	libavfilter/avfilter.h
INSTALL	libavfilter/avfiltergraph.h
INSTALL	libavfilter/buffersink.h
INSTALL	libavfilter/buffersrc.h
INSTALL	libavfilter/version.h
INSTALL	libavfilter/libavfilter.pc
INSTALL	libavformat/avformat.h
INSTALL	libavformat/avio.h
INSTALL	libavformat/version.h
INSTALL	libavformat/libavformat.pc
INSTALL	libavcodec/avcodec.h
INSTALL	libavcodec/avfft.h
INSTALL	libavcodec/d3d11va.h
INSTALL	libavcodec/dirac.h
INSTALL	libavcodec/dv_profile.h
INSTALL	libavcodec/dxva2.h
INSTALL	libavcodec/qsv.h
INSTALL	libavcodec/vaapi.h
INSTALL	libavcodec/vda.h
INSTALL	libavcodec/vdpau.h
INSTALL	libavcodec/version.h
INSTALL	libavcodec/vorbis_parser.h
INSTALL	libavcodec/xvmc.h
INSTALL	libavcodec/libavcodec.pc
INSTALL	libavresample/avresample.h
INSTALL	libavresample/version.h
INSTALL	libavresample/libavresample.pc
INSTALL	libswscale/swscale.h
INSTALL	libswscale/version.h
INSTALL	libswscale/libswscale.pc
INSTALL	libavutil/adler32.h
INSTALL	libavutil/aes.h
INSTALL	libavutil/attributes.h
INSTALL	libavutil/audio_fifo.h
INSTALL	libavutil/avassert.h
INSTALL	libavutil/avstring.h
INSTALL	libavutil/avutil.h
INSTALL	libavutil/base64.h
INSTALL	libavutil/blowfish.h
INSTALL	libavutil/bswap.h
INSTALL	libavutil/buffer.h
INSTALL	libavutil/channel_layout.h
INSTALL	libavutil/common.h
INSTALL	libavutil/cpu.h
INSTALL	libavutil/crc.h
INSTALL	libavutil/dict.h
INSTALL	libavutil/display.h
INSTALL	libavutil/downmix_info.h
INSTALL	libavutil/error.h
INSTALL	libavutil/eval.h
INSTALL	libavutil/fifo.h
INSTALL	libavutil/file.h
INSTALL	libavutil/frame.h
INSTALL	libavutil/hmac.h
INSTALL	libavutil/hwcontext.h
INSTALL	libavutil/hwcontext_cuda.h
INSTALL	libavutil/hwcontext_dxva2.h
INSTALL	libavutil/hwcontext_qsv.h
INSTALL	libavutil/hwcontext_vaapi.h
INSTALL	libavutil/hwcontext_vdpau.h
INSTALL	libavutil/imgutils.h
INSTALL	libavutil/intfloat.h
INSTALL	libavutil/intreadwrite.h
INSTALL	libavutil/lfg.h
INSTALL	libavutil/log.h
INSTALL	libavutil/macros.h
INSTALL	libavutil/mathematics.h
INSTALL	libavutil/md5.h
INSTALL	libavutil/mem.h
INSTALL	libavutil/opt.h
INSTALL	libavutil/parseutils.h
INSTALL	libavutil/pixdesc.h
INSTALL	libavutil/pixfmt.h
INSTALL	libavutil/random_seed.h
INSTALL	libavutil/rational.h
INSTALL	libavutil/replaygain.h
INSTALL	libavutil/samplefmt.h
INSTALL	libavutil/sha.h
INSTALL	libavutil/stereo3d.h
INSTALL	libavutil/time.h
INSTALL	libavutil/version.h
INSTALL	libavutil/xtea.h
INSTALL	libavutil/lzo.h
INSTALL	libavutil/avconfig.h
INSTALL	libavutil/libavutil.pc
```



### bmdtoolsのコンパイル、インストール





{% capture text %}
##### Build

```sh
# git clone git://github.com/lu-zero/bmdtools
# cd bmdtools
# make SDK_PATH=<path where you unpacked the decklink sdk>/<Target OS>/include
```

Note: The SDK currently supports Linux and MacOSX. 
Thus the `<Target OS>` can be either `Linux` or `Mac`.

##### Install

```sh
# cp bmdcapture bmdplay /usr/local/bin
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Home · lu-zero/bmdtools Wiki](https://github.com/lu-zero/bmdtools/wiki)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



まずは`git clone`でソースコードを取得する。

```sh
$ git clone https://github.com/lu-zero/bmdtools.git
```

Blackmagic SDKの中から _include_ ディレクトリを bmdtools ディレクトリにコピーする。

```sh
$ cp -r ../Blackmagic\ DeckLink\ SDK\ 10.9.5/Linux/include/ .
```

`make SDK_PATH=include`でビルドする。


ビルドは通ったが

```sh
$ ./bmdcapture 
./bmdcapture: error while loading shared libraries: libx265.so.130: cannot open shared object file: No such file or directory
```

と言われてしまい実行できない。


{% capture text %}
原因はライブラリのパスが正しく設定されていないためです．
ライブラリパスが通っているかどうかは ldd コマンドで確認できます．not found となっているものは正しく設定されていないです．

```sh
$ ldd ./a.out
...
libcudart.so.7.0 => not found
...

```

対応としては，環境変数設定としてライブラリパスを通してください．
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ジョブ実行時に error while loading shared libraries が出力されて失敗する場合の対応方法 \| TSUBAME計算サービス](http://tsubame.gsic.titech.ac.jp/node/1340)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




`ldd`をしてみると、

```sh

$ ldd bmd*
bmdcapture:
	linux-vdso.so.1 =>  (0x00007fffd939d000)
	libx265.so.130 => not found
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007f400e339000)
	libfdk-aac.so.1 => not found
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f400e02f000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f400de2b000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f400dc0d000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f400d88b000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f400d4c1000)
	/lib64/ld-linux-x86-64.so.2 (0x000055a7303e3000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f400d2aa000)
bmdgenlock:
	linux-vdso.so.1 =>  (0x00007ffe121f0000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f3329975000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f3329758000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f332938d000)
	/lib64/ld-linux-x86-64.so.2 (0x0000563c40872000)
bmdplay:
	linux-vdso.so.1 =>  (0x00007ffdde3e0000)
	libx265.so.130 => not found
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007fd3f3eb9000)
	libfdk-aac.so.1 => not found
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fd3f3baf000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fd3f39ab000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fd3f378d000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fd3f340b000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fd3f31f5000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd3f2e2a000)
	/lib64/ld-linux-x86-64.so.2 (0x000055fa7c802000)
```

_libx265.so.130_ 及び _libfdk-aac.so.1_ のリンク切れが起きていることが確認できる。
これらを捜索すると

```sh
$ ls /usr/local/lib/
libavcodec.a   libavformat.a    libfdk-aac.a   libfdk-aac.so.1      libopus.la    libopus.so.0.6.1  libx264.so      libx265.so      pkgconfig
libavdevice.a  libavresample.a  libfdk-aac.la  libfdk-aac.so.1.0.0  libopus.so    libswscale.a      libx264.so.152  libx265.so.130  python2.7
libavfilter.a  libavutil.a      libfdk-aac.so  libopus.a            libopus.so.0  libx264.a         libx265.a       libyasm.a       python3.5
```

`/usr/local/lib` にいた。


{% capture text %}
ライブラリのパスを通すために `LD_LIBRARY_PATH` にパスを追加すると解消されます。

```sh
export LD_LIBRARY_PATH="/usr/local/lib"
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Linux で error while loading shared libraries: が表示されて実行できない場合 \[ へっぽこSEのメモ帳 \]](http://pucyan.blog77.fc2.com/blog-entry-73.html)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





と書いてあるが、`LD_LIBRARY_PATH` を設定しても変わらず。






{% capture text %}
What you want is: `LDFLAGS="-L$DIR/lib/ -R$DIR/lib/"`

> "libevent not found"

I trusted you to read the man page, but you didn't. 
The `-R` flag means `RUNPATH` to linker on Solaris, 
but it means something else to Linux linker.

What you want then is:

```
LDFLAGS="-L$DIR/lib/ -Wl,--rpath=$DIR/lib/"
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Employed Russian](https://stackoverflow.com/users/50617/employed-russian) / [linux - How to do runtime linking in make using LDFLAGS -R option, or some other way - Stack Overflow](https://stackoverflow.com/a/17255045)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
それぞれのバイナリをテキストエディタ眺めてみると、ライブラリを見つけてもらえない場合は、 _/usr/local/imlib2/lib_ という文字が含まれてないことがわかりました。

そこで `objdump -p binary` で調べてみると、見つけてもらえる場合は、以下のようなのが含まれていました。

```
Dynamic Section:
  RPATH                /usr/local/imlib2/lib
```

リンク時に `gcc` に `-Wl,-rpath /path/to/lib` を渡すことで、RPATH を渡せることがわかり無事解決しました。

```
-Wl,-rpath /usr/local/imlib2/lib
```

無事リンクできて、めでたしめでたし。

参考リンク:

とあるエンジニアの備忘log: Shared Object 入門
: [http://masahir0y.blogspot.jp/2013/01/shared-object.html](http://masahir0y.blogspot.jp/2013/01/shared-object.html)

知っていると便利な gcc のオプション
: [http://www.unixuser.org/~euske/doc/gccopts/](http://www.unixuser.org/~euske/doc/gccopts/)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [woremacx](https://qiita.com/woremacx) / [変な場所にインストールした共有ライブラリを使ってもらえないとき対策(RPATH) - Qiita](https://qiita.com/woremacx/items/41bfd22f35f0f3b3c143)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





つまり `-L/usr/local/lib -Wl,-rpath /usr/local/lib` をコンパイルオプションに指定すればよいと。
ここで`Makefile`を見てみると



{% capture text %}
```make
LDFLAGS  = $(ELDFLAGS)
...
all: $(PROGRAMS)

bmdcapture: bmdcapture.cpp $(COMMON_FILES)
        $(CXX) -o $@ $^ $(CXXFLAGS) $(LDFLAGS)

bmdplay: bmdplay.cpp $(COMMON_FILES)
        $(CXX) -o $@ $^ $(CXXFLAGS) $(LDFLAGS)

bmdgenlock: genlock.cpp $(SDK_PATH)/DeckLinkAPIDispatch.cpp
        $(CXX) -o $@ $^ $(CXXFLAGS) $(LDFLAGS)
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
Makefile
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
`./configure --help`を見てください。

> LDFLAGS linker flags, e.g. -L if you have libraries in a
> nonstandard directory
> 
> LIBS linker flags, e.g. -l.{a,so} if you have nonstandard
> libraries to link

とあります。

これらの変数は最終的に _Makefile_ で

```make
$(CC) $(LD_FLAGS) -o taget $(LIBS)
```

のように使われます。
ですから`gcc`に与えるオプションをそのままわたせばいいと思います。

一般的な使い方としては，
ライブラリのある位置を`LD_FLAGS`で指定して，
特別にライブラリを追加してリンクする時は`LIBS`を指定すればいいのではないでしょうか?
（ただ、`LIBS`を指定するのがどんな時なのかちょっと想像できませんね）

以下のようにすれば`/home/hoge/lib`以下のライブラリも検索するようになります。

```
LD_FLAGS="-L/home/hoge/lib" ./configure
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by dario [configureするときのLDFLAGSやLIBSとは - QA@IT](https://qa.atmarkit.co.jp/q/334)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




`LDFLAGS` が `ELDFLAGS` で上書きされている。ので、この場合`ELDFLAGS="-L/usr/local/lib -Wl,-rpath /usr/local/lib"`とすればよいらしい。

```sh
make SDK_PATH=include ELDFLAGS="-L/usr/local/lib -Wl,-rpath /usr/local/lib"
```






`ldd` で確認してみる。

```sh
$ ldd bmd*
bmdcapture:
	linux-vdso.so.1 =>  (0x00007ffde248e000)
	libx265.so.130 => /usr/local/lib/libx265.so.130 (0x00007fab1f8da000)
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007fab1f52e000)
	libfdk-aac.so.1 => /usr/local/lib/libfdk-aac.so.1 (0x00007fab1f274000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fab1ef50000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fab1ed4c000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fab1eb2e000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fab1e7ac000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fab1e3e2000)
	/lib64/ld-linux-x86-64.so.2 (0x000055b3520be000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fab1e1cb000)
bmdgenlock:
	linux-vdso.so.1 =>  (0x00007ffe25b86000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f9843e49000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f9843c2c000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9843861000)
	/lib64/ld-linux-x86-64.so.2 (0x000055cae72c6000)
bmdplay:
	linux-vdso.so.1 =>  (0x00007fffbe1fd000)
	libx265.so.130 => /usr/local/lib/libx265.so.130 (0x00007f9ae3b0a000)
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007f9ae375e000)
	libfdk-aac.so.1 => /usr/local/lib/libfdk-aac.so.1 (0x00007f9ae34a4000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f9ae3180000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f9ae2f7c000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f9ae2d5e000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f9ae29dc000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f9ae27c6000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9ae23fb000)
	/lib64/ld-linux-x86-64.so.2 (0x0000559eaa8bd000)
```




いけたらしい。`make install`。

```sh
sudo make SDK_PATH=include ELDFLAGS="-L/usr/local/lib -Wl,-rpath /usr/local/lib" install
mkdir -p //usr/bin
cp bmdcapture bmdplay bmdgenlock //usr/bin
```


### libav もだった。

```sh
$ ldd avconv
	linux-vdso.so.1 =>  (0x00007ffc90ffd000)
	libasound.so.2 => /usr/lib/x86_64-linux-gnu/libasound.so.2 (0x00007f6bd056e000)
	libx265.so.130 => not found
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007f6bd01c1000)
	libfdk-aac.so.1 => not found
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f6bcfeb8000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f6bcfc9a000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f6bcf8d0000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f6bcf6cc000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f6bcf4c3000)
	/lib64/ld-linux-x86-64.so.2 (0x0000558ef1b06000)
```

なんということだ。アンインストールしてコンパイルをしなおす。

```sh
$ sudo make uninstall
$ make clean
```

`./configure` を変更してビルド

```sh
 $ ./configure --enable-gpl --enable-nonfree --enable-libx264 --enable-libx265 --enable-libfdk-aac --extra-ldflags="-L/usr/local/lib -Wl,-rpath /usr/local/lib"
 $ make
```

```sh
$ ldd avconv
	linux-vdso.so.1 =>  (0x00007ffc71fcc000)
	libasound.so.2 => /usr/lib/x86_64-linux-gnu/libasound.so.2 (0x00007fd8e11a1000)
	libx265.so.130 => /usr/local/lib/libx265.so.130 (0x00007fd8e0b2e000)
	libx264.so.152 => /usr/local/lib/libx264.so.152 (0x00007fd8e0781000)
	libfdk-aac.so.1 => /usr/local/lib/libfdk-aac.so.1 (0x00007fd8e04c8000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fd8e01bf000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fd8dffa1000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd8dfbd7000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fd8df9d3000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007fd8df7ca000)
	/lib64/ld-linux-x86-64.so.2 (0x000055a733372000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fd8df448000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fd8df231000)
```

インストールする。
```sh
$ sudo make install
```



{% capture text %}
_/usr/local/lib_ の共有ライブラリ(.so)を参照させるには以下のどちらかを選択

#### 環境変数 `LD_LIBRARY_PATH` を設定する

_.bash_profile_ に以下を記述

```sh
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```

#### _/etc/ld.so.conf_ に追加

```sh
echo "/usr/local/lib" > /etc/ld.so.conf.d/usr-local-lib.conf
```

再起動するか `# ldconfig -v` を実行する

昨日のXalanで _/usr/local/lib/libxalan-c.so.110_ を参照してもらえなかったマシンがあったので。

\#マシンごとに設定が違うのやられるなぁ

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[/usr/local/libの共有ライブラリ(.so)を参照しないとき - 計算機と戯れる日々](http://d.hatena.ne.jp/n9d/20080820/1219228809)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これをしてしまおう。


```sh
 $ sudo vi /etc/ld.so.conf.d/usr-local-lib.conf
```



以下を書き込んで保存

```
/usr/local/lib
```

設定を反映。更新されているか確認する。


```sh
$ sudo ldconfig -v 
/usr/local/lib:
	libopus.so.0 -> libopus.so.0.6.1
	libx265.so.130 -> libx265.so.130
	libx264.so.152 -> libx264.so.152
	libfdk-aac.so.1 -> libfdk-aac.so.1.0.0
```







## 動作確認

ひとまず、`avconv`を使って録画してみた。
```sh
$ bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -o strict=experimental:syncpoints=none -f pipe:1 | avconv -vsync passthrough -y -i - -vcodec libx264 test.avi
```

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/bmdtools/05_capture.png %}{% endcapture %}
{% capture caption %}
デスクトップを撮影したものを再生している。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

