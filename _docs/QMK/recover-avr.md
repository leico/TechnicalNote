---
layout : post
title  : 文鎮化した自作キーボードをArduinoを使って復帰させる
date   : 2018/08/23
lastchange : 2018-08-27 22:00:51.
tags   :
  - 自作キーボード
  - "Let's Split - Vitamins included"
  - AVR
  - atmega32u4
  - QMK Toolbox
  - avrdude
---

## QMK Toolbox を macOS で動かそうとしていたら書き込みミス

書き込み途中にフリーズして強制リセットで文鎮化してしまった。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/recover-avr/01_freeze.png %}{% endcapture %}
{% capture caption %}

この状態でフリーズして帰らぬキーボードになった

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

幸いAVRのIOポートが基板に存在していたのでピンを生やしてAVRライタから復帰させることができた。
そのログ。

参考:

* [qmk/qmk_toolbox: A Toolbox companion for QMK Firmware](https://github.com/qmk/qmk_toolbox)
* [avrdude flashing error on AVR GCC 8.2.0 · Issue #3657 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/3657)
* [Lets Split Sockets/Vitamins failing with default build : olkb](https://www.reddit.com/r/olkb/comments/939tz6/lets_split_socketsvitamins_failing_with_default/)
* [ArduinoでHEXファイルを書き込む : はやねさん工房](http://blog.livedoor.jp/hymne333/archives/4710525.html)
* [ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
* [Mac で Arduino Leonardo / SparkFun Pro Micro で 焼ける環境を用意する - Qiita](https://qiita.com/hidenorly/items/283796be24faac861093)


## 必要なもの

* Arduino Uno または互換機
* ピンヘッダ 6本
* オスメスのジャンパワイヤー 6本
* 以下の全て
    * はんだごて
    * はんだ
    * はんだ付けできる程度の能力
* または以下のもの
    * スルーホールテストワイヤー(ジャンパー)
* 以下のいずれか
    * 間違えずにジャンパワイヤーを結線する程度の能力
    * 間違えても結線を見直して修正できる程度の能力


## Arduino 互換機を AVR ライタにする

[Arduino を AVR ライタにする]({{site.github.url}}{% link _docs/Arduino/avr-isp.md %})

と別記事を書いた。こちらを参考に Arduino を AVR ライタにする。








## 必要なバイナリやライブラリをインストールする

{% capture text %}

## macOS

If you're using [homebrew](https://brew.sh/), you can use the following commands:

```sh
brew tap osx-cross/avr
brew tap PX4/homebrew-px4
brew update
brew install avr-gcc
brew install dfu-programmer
brew install gcc-arm-none-eabi
brew install avrdude
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk/qmk_toolbox: A Toolbox companion for QMK Firmware](https://github.com/qmk/qmk_toolbox)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上を実行すれば事足りる・・・のだが、現在 osx/cross に入っている `avr-gcc` は 8.2.0 で、
以下のように 8.2.0 だと範囲外のアドレスが参照されて転送できない状況になっている。

* [avrdude flashing error on AVR GCC 8.2.0 · Issue #3657 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/3657)
* [Lets Split Sockets/Vitamins failing with default build : olkb](https://www.reddit.com/r/olkb/comments/939tz6/lets_split_socketsvitamins_failing_with_default/)

解決する方法として `avr-gcc8.1.0` を利用する手段が発見されている。

* [avrdude flashing error on AVR GCC 8.2.0 · Issue #3657 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/3657#issuecomment-414486082)
* [Let's Split Vitamins Included Issues : olkb](https://www.reddit.com/r/olkb/comments/96r0os/_/e4djoqi/)

のだが、 osx-cross/avr では `avr-gcc8.1.0` を選択することができない。
なので osx-cross/avr をフォークして `avr-gcc8.1.0` を選択できる formulae を作成した。

[leico/homebrew-avr: Homebrew AVR Toolchain](https://github.com/leico/homebrew-avr)

上記のコマンドのインストールが全て終わった後、 osx-cross/avr を untap し、 leico/avr を tap する。

```sh
$ brew untap osx-cross/avr
$ brew tap leico/avr
```

`avr-gcc` をアンインストールして `avr-gcc8.1.0` をインストールする。

```sh
$ brew uninstall avr-gcc && brew install avr-gcc@8_1
```

ここまででソフトウェア側の準備は完了。



## ISP 化した Arduino とキーボードの AVR ポートを結線する

AVR用ICSPポートにピンヘッダを取り付けるのだが、その前に本当にAVR用のポートなのか確認した。
確認方法として、AVRチップの型番で調べ、穴と該当するチップの足が接続されているかをテスターで確かめた。

載っているチップは印字から ATmega32u4 というのが判明したので、データシートから足の該当する位置を探る。

| ピン番号 | ポート |
|----------|--------|
| 9        | SCK    |
| 10       | MOSI   |
| 11       | MISO   |
| 13       | RESET  |

ひとまずこの辺が繋がっているかを確認する。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/recover-avr/02_check.jpg %}{% endcapture %}
{% capture caption %}

足に当てるのがとても細かい作業になるが、不可能ではない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



確認できたらISP と接続するためにキーボードのAVR用ICSPポートにピンヘッダをつける。
Let's Split Vitamins includedはスルーホールで表向きにも裏向きにもピンヘッダを取り付けることができるが、
表につけると明らかにスイッチと干渉するため、裏向きにピンヘッダを取り付けた。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/recover-avr/03_header.jpg %}{% endcapture %}
{% capture caption %}

裏側に飛び出た部分が出てくる

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

キーと干渉はなくなったが、欠点として通常の ICSP と左右反転するので注意しなければならない。


{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/recover-avr/04_ICSP.svg %}{% endcapture %}
{% capture caption %}

通常の接続とは異なるので **すごく注意する**

四角のフットプリントが MISO 、その裏側に VST(今回の場合5V)、逆サイドにRESET と GND がくる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


最終的にこのような接続になる。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/recover-avr/05_wire.jpg %}{% endcapture %}
{% capture caption %}

ちょっとごちゃごちゃしている。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## `avrdude` で接続確認


Arduino ISP から `avrdude` を使って書き込みができそうか調べる。

{% capture text %}

ArduinoとAVRが正しく配線されていることを確認してPCとArduinoを接続します。
以下のコマンドを打ち込んで実行してください。

```sh
avrdude -c avrisp -P COMポート番号 -b 19200 -p マイコン名
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoでHEXファイルを書き込む : はやねさん工房](http://blog.livedoor.jp/hymne333/archives/4710525.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


できそうだが、このコマンドの意味はなんだろう。



{% capture text %}


#### 6-4-3. デバイスIDを確認する

それでは、ここからは、実際にArduinoISPとavrdudeを使って、AVRマイコンに読み書きしてみましょう。

---

次にコマンドラインから次の様に入力して、エンターキーを押してください。
コマンドラインオプションは大文字と小文字で別の意味になるので、注意してください。

```sh
avrdude -c avrisp -P COM7 -b 19200 -p m328p
```

ただし、 **COM7の部分は、ArduinoISPを用いたライタが接続されているCOM番号** です。
私の環境ではCOM7にライタが接続されていますが、 **ご自分の環境に合わせて書き換えてください** 。
これ以降の説明では、-Pオプションの部分は、全てご自分の環境に合わせて打ち換えてください。

なお、各オプションは、次の様な意味になっています。

-c avrisp
: ライタとしてArduinoISP(またはAVR ISP)を指定している。

-P COM7
: ライタが接続されているCOMポートを指定している。先ほど説明したように、
COM7の部分は自分の環境に合わせて書き換える必要がある。

-b 19200
: ライタとの通信速度を19200bpsに指定している。
(ArduinoISPは19200bpsで動作するように作られているため)

-p m328p
: AVRマイコンの種類をATmega328Pと指定している。

機器の接続が正常で、コマンドラインオプションの指定も間違っていなければ、
次の様にDevice signature(デバイスID)が読み出されます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





このページがとても参考になる。これによれば接続チェックとしてデバイスIDを取得できるらしい。






{% capture text %}


## Arduino ISP＋avrdudeでのhexファイルの焼き込み

```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.wchusbserial1410 -D -U flash:w:atmega32u4.hex:i
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Mac で Arduino Leonardo / SparkFun Pro Micro で 焼ける環境を用意する - Qiita](https://qiita.com/hidenorly/items/283796be24faac861093)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





ATmega32u4 の場合このような引数になるらしい。設定を変更して実行してみる。


```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

接続が正常でチップが生きていればこのように出力される。
接続が間違っている場合、

```
avrdude: Device signature = 0x1e9587 (probably m32u4)
```

この部分が `0x000000` や `0xffffff` といった値になることが多い。
接続をしっかりと確認すること。接続を厳密に10回確認して合っている場合はチップが死んでいる可能性が高い。








## ヒューズビットが正しいか正常な方と比べて確認する

ここまででワイヤーの接続が正しいということが証明されている。
書き換えられてしまっていると一番マズイことになるヒューズビットから確認する。




{% capture text %}

#### 6-4-4.ヒューズビット(ヒューズバイト)を読み出す

次にヒューズビットを読んでみます。ヒューズビットとは、例えば「外部発振器を使うか、内臓CR発振器を使うか」とか「リセットピンの機能を有効にするか、GPIOピンとして使うか」など、AVRマイコンの動作の設定を書き込む不揮発性メモリで、フラッシュメモリやEEPROMとは別のメモリ空間にあります。AVRマイコンの種類ごとに、ヒューズビットで設定する項目の内容や数が異なります。

---

下位ヒューズバイトを読むには、コマンドラインで次の様に打ち込んでエンターキーを押します。(例によって、COM7の部分は環境により異なります)

```sh
avrdude -c avrisp -P COM7 -b 19200 -p m328p -U lfuse:r:con:h
```
ここで、-Uオプションは、対象メモリとして下位ヒューズバイト(lfuse)を、
操作の種類として読み出し(r)を、ファイルとして標準入出力(con)を、ファイルフォーマットとして16進表記(h)を指定します。
つまり、下位ヒューズバイトを読んで、その内容をコンソール画面に16進数で表示するという意味です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



同ページに、 `-U` オプションに関しても詳細に記されている。


{% capture text %}

#### 6-3-6.-Uオプション(読み書きの実行)

-Uオプションは、AVRマイコンの内部メモリに対して、
読み書きなどを実行するオプションで、書式は次のようになっています。

```sh
-U memtype:op:filename[:format]
```

memtypeは、操作の対象のメモリの種類を指定する部分です。表3に、主なmemtypeの文字列を示します。

**_表3、memtype文字列とメモリの種類_**

文字列
: メモリの種類

`flash`
: フラッシュメモリ(プログラムを書き込むメモリ)

`fuse` 
: ヒューズバイト(ヒューズバイトが1バイトのみのマイコン用) 

`hfuse`
: 上位ヒューズバイト(fuse high byte)

`lfuse`
: 下位ヒューズバイト(fuse low byte)

`efuse`
: 拡張ヒューズバイト(extended fuse byte)

`lock`
: ロックビットバイト

`signature` 
: デバイスID

opは、操作の種類を表わします。表4に、opの文字列の一覧を示します。


**_表4、op文字列と操作の種類_**

文字列
: 操作の種類

`r`
: 指定されたメモリを読み、その内容を指定されたファイルに書き込む。

`w`
: 指定されたファイルを読み、その内容を指定されたメモリに書き込む。

`v`
: 指定されたファイルと、指定されたメモリを読み、ベリファイ(内容が一致している事の確認)を行う。




filenameはファイルの名前(パスを含めることも可)を指定する部分です。




formatはファイルの形式(フォーマット)を指定する部分で、省略する事ができます。
表5に、主なformatの文字列と、その意味の一覧表を示します。

**_表5、format文字列とファイルのフォーマット_**

文字列
: ファイルのフォーマット

`i`
: IntelのHex形式

`s`
: モトローラのsレコード形式

`r`
: 生バイナリーフォーマット。フラッシュメモリの場合はリトルエンディアン。

`e`
: ELF(リンカの出力ファイル形式)。入力ファイルとしてのみ指定できる。

`m`
: イミディエート(即値)モード。-Uオプションのfilenameフィールドにバイト値をコンマかスペースで指定する。
値が0xで始まると16進数、0で始まると8進数、それ以外なら10進数と解釈される。
このモードは、ヒューズバイトの指定の際に使うと、たった1バイトのために、わざわざファイルを作らなくて済むので、便利。

`a` 
: フォーマットを自動検出する。ファイルを入力として使うときのみ指定できる。

formatを指定しないと、入力ファイルに関しては、フォーマットを自動検出します。
出力ファイルに関しては、生バイナリフォーマットが選択されます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

各ヒューズビットを読み込み、正常な方と突き合わせて同じになっているか確認する。
それぞれキーボードと ISP を繋ぎ替えて2回同じコマンドを叩いて比較検証する。


### 下位ヒューズビットの読み出し

```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200 -U lfuse:r:lfuse:h

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading lfuse memory:

Reading | ################################################## | 100% 0.02s

avrdude: writing output file "lfuse"

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

```sh
$ cat lfuse 
0xff
```

両方とも `0xff` だった。大丈夫っぽい。




### 上位ヒューズビットの読み出し

```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200 -U hfuse:r:hfuse:h

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading hfuse memory:

Reading | ################################################## | 100% 0.02s

avrdude: writing output file "hfuse"

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

```sh
$ cat hfuse
0xd8
```

両方とも `0xd8` だった。ということはヒューズビットは出荷時と同じということだ。
最初の状態でUSBから見れていたので、ここは異常がない。








## ロックビットも確認する

{% capture text %}

#### 6-4-5.ロックビット(ロックビットバイト)を読み込む

AVRマイコンにはロックビットという記憶領域があります。これは、フラッシュメモリの内容や、
ヒューズバイトの内容を、間違って書き換えないように、ロックするための設定を書き込む領域です。

---

ロックビットを読むには、次のコマンドを実行してください。新品のATmega328Pの場合は3FHが読めるはずです。

```sh
avrdude -c avrisp -P COM7 -b 19200 -p m328p -U lock:r:con:h
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これも無事な方と比べてみる。

```sh

$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200 -U lock:r:lock:h

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading lock memory:

Reading | ################################################## | 100% 0.02s

avrdude: writing output file "lock"

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.

$ cat lock 
0x3f
```

atmega32u4 でも `0x3f` が正常値らしい。これも正常な方と変わらなかった。異常なし。


## 正常な方から工場出荷時のファームを吸い出して異常な方に書き込む

{% capture text %}

#### 6-4-9.フラッシュメモリを読み出してファイルに記録する

フラッシュメモリに書き込まれた情報を読み出し、.hexファイルに記録するには次のコマンドを実行します。

```sh
avrdude -c avrisp -P COM7 -b 19200 -p m328p -U flash:r:test.hex:i
```

こうする事で、フラッシュメモリの内容が全て読み出され、
"test.hex"というファイル名の.hexファイルに記録されます。
このtest.hexを別のATmega328Pに書き込めば、
フラッシュメモリの内容が全てコピーできる事になります。

Arduinoのスケッチが書かれたATmega328Pのフラッシュメモリを読み出すと、
ブートローダとスケッチの情報がまとめて読み出されます。
その.hexファイルを別のATmega328Pに書き込めば、ブートローダとスケッチの書き込みが同時に行われるため、
量産時に手間を省く事ができます。(他にもヒューズバイトとロックビットバイトの書き込みは必要)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ArduinoISPを汎用AVRライタとして使う(2) - しなぷすのハード製作記](https://synapse.kyoto/tips/ArduinoISP_AVRWriter/page002.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ここまでのチェックが正常であれば、これを行って異常な方に書き込めば元に戻りそうだ。
ついでにバックアップを取っておけば、こういういざという時に使えそうだ。

### 正常な方を ISP に接続し、ファームを吸い出す

```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200 -U flash:r:backup.hex:i

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading flash memory:

Reading | ################################################## | 100% 24.59s

avrdude: writing output file "backup.hex"

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

それなりに時間がかかる。 **絶対に途中で抜き差ししないこと。** 
これで _backup.hex_ というファイルができているはずだ。

### 異常な方を ISP に接続し、ファームを書き込む

```sh
$ avrdude -p atmega32u4 -c avrisp -P /dev/tty.usbserial-DA00X4WO -b 19200 -U flash:w:backup.hex:i

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.05s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: NOTE: "flash" memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file "backup.hex"
avrdude: writing flash (32762 bytes):

Writing | ################################################## | 100% 40.98s

avrdude: 32762 bytes of flash written
avrdude: verifying flash memory against backup.hex:
avrdude: load data flash data from input file backup.hex:
avrdude: input file backup.hex contains 32762 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 24.59s

avrdude: verifying ...
avrdude: 32762 bytes of flash verified

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

こちらもそれなりに時間がかかる。 **絶対に途中で抜き差ししないこと。** 
これで USB 接続が復活しているはずだ。


### キーボードとUSB接続して正常か確認する



```sh
$ ls /dev/tty*
/dev/tty                          /dev/ttyr9    /dev/ttyu2
/dev/tty.Bluetooth-Incoming-Port  /dev/ttyra    /dev/ttyu3
/dev/tty.MALS                     /dev/ttyrb    /dev/ttyu4
/dev/tty.SOC                      /dev/ttyrc    /dev/ttyu5
/dev/tty.usbmodem1451	            ...
```

`/dev/tty.usbmodem1451` が見えるようになった。よかった、復活した。

AVR ISP と接続できる端子もついててよかった。

