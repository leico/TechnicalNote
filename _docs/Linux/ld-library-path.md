---
layout : post
title  : "Linux: ライブラリの動的リンクでエラーが出た場合の対処方法"
date : 2017/10/18
lastchange : 2017-10-21 21:32:32.
tags   :
  - bmdtools
  - "LD_LIBRARY_PATH"
  - g++
  - gcc
  - -R
  - "/usr/local/lib"
---

## コマンド実行時にリンカーエラー

これは`bmdtools`をビルドした時の話。
_/usr/local/lib_ がディストリビューションのよってはライブラリ検索パスに追加されていないため

```sh
$ ./bmdcapture 
./bmdcapture: error while loading shared libraries: libx265.so.130: cannot open shared object file: No such file or directory
```

と言われてしまい実行できない。
これを解決するまでの話。

参考:

* [ジョブ実行時に error while loading shared libraries が出力されて失敗する場合の対応方法 \| TSUBAME計算サービス](http://tsubame.gsic.titech.ac.jp/node/1340)
* [/usr/local/libの共有ライブラリ(.so)を参照しないとき - 計算機と戯れる日々](http://d.hatena.ne.jp/n9d/20080820/1219228809)
* [linux - How to do runtime linking in make using LDFLAGS -R option, or some other way - Stack Overflow](https://stackoverflow.com/a/17255045)
* [変な場所にインストールした共有ライブラリを使ってもらえないとき対策(RPATH) - Qiita](https://qiita.com/woremacx/items/41bfd22f35f0f3b3c143)
* [configureするときのLDFLAGSやLIBSとは - QA@IT](https://qa.atmarkit.co.jp/q/334)



## ライブラリ検索パスに _/usr/local/lib_ を追加する

これは

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


ライブラリ検索パスを設定する方法はいくつかある。
_/usr/local/lib_ をパスに追加するオススメ順に紹介する。








### _/etc/ld.so.conf_ に設定ファイルを追加する



{% capture text %}

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

これは全ユーザ/グループが影響を受ける設定方法になる。
なので予期せぬバグが発生することがあるが、 _/usr/local/lib_ 
であれば一般的に利用されるインストール先なので問題ないと判断した。

_/usr/local/lib_ に独自開発したライブラリをインストールする場合などは気をつけねばならない。


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




### `LD_LIBRARY_PATH` にパスを追加する

{% capture text %}

#### 環境変数 `LD_LIBRARY_PATH` を設定する

_.bash_profile_ に以下を記述

```sh
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[/usr/local/libの共有ライブラリ(.so)を参照しないとき - 計算機と戯れる日々](http://d.hatena.ne.jp/n9d/20080820/1219228809)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




これも一般的な方法。しかし

1. `env`した時にそもそも`LD_LIBRARY_PATH`が存在していなかった
2. _.bash\_profile_ の内容が肥大化していく
3. 動的リンク用の変数なのでビルド時指定だけでは意味がない
4. _/usr/local/lib_ であれば全体に適用して大丈夫だろうと判断した

といった理由で採用しなかった。




#### コンパイル時に`LDFLAGS="-L/usr/local/lib -Wl,-rpath /usr/local/lib"`を指定する



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



`-Wl,-rpath`をコンパイル時に指定してあげれば、動的リンクするライブラリの場所が
生成されるバイナリに書き込まれるので環境変数などでパスが通ってなくても問題ない。

なぜ`LDFLAGS`かというと

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


ただ、この方法ではソフトウェアをビルドする度に忘れず引数の指定をしなければならなくなる。

