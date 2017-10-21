---
layout : post
title  : make installしたソフトウェアをアンインストールする
date : 2017/10/21
lastchange : 2017-10-21 20:30:53.
tags   :
  - make
  - make install
  - make uninstall
  - make clean
---

## 自分でコンパイルしたソフトウェアのアンインストール方法

参考 : 
* [RPM を使わずに make install した場合のアンインストール方法](https://futuremix.org/2009/01/configure-make-uninstall)



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


他、インストールした時に表示されるものを残しておくとどこにインストールされているかが分かる。

これはlibx265を`make install`した時のログ出力。

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
