---
layout : post
title  : "Lets'split - vitamins included にデフォルトのキーマップを書き込む"
date   : 2018/08/23
lastchange : 2018-08-24 00:21:56.
tags   :
  - 自作キーボード
  - "Let's Split - Vitamins included"
  - AVR
  - atmega32u4
  - QMK Toolbox
  - avrdude
---

## QMK firmware と `avrdude` を利用して、コマンドラインで書き込む

[文鎮化した自作キーボードをArduinoを使って復帰させる]({{site.github.url}}{% link _docs/QMK/recover-avr.md %})

このように自分の場合、 QMK Toolbox が使い物にならなかったので
コマンドラインでファームを書き込むことになった。 
macOS 10.13 系の人で素人に毛が生えた程度の BSD/AVR 使いの方は要注意です。

参考:


## QMK firmware のダウンロード

{% capture text %}

Otherwise, you can either download it directly 
([zip](https://github.com/qmk/qmk_firmware/zipball/master), 
[tar](https://github.com/qmk/qmk_firmware/tarball/master)), 
or clone it via git (`git@github.com:qmk/qmk_firmware.git`), 
or https (`https://github.com/qmk/qmk_firmware.git`).

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[QMK Firmware](https://docs.qmk.fm/#/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

zip をリポジトリからダウンロードするか `git clone` するか、好きな方法で QMK firmware
を取得する。

リポジトリこちら

[qmk/qmk_firmware: keyboard controller firmware for Atmel AVR and ARM USB families](https://github.com/qmk/qmk_firmware)

## Let's Split - Vitamins included 用のファームを作成して書き込む

Let's Split - Vitamins included の設定ファイルだが、ここに情報があった。

{% capture text %}

For reference, this is the board specifically that I'm talking about.

<https://github.com/qmk/qmk_firmware/tree/master/keyboards/vitamins_included>

...
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Some questions about The Let's Split Vitamins Included : olkb](https://www.reddit.com/r/olkb/comments/8zsu7x/some_questions_about_the_lets_split_vitamins/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このリンク先が基本的な利用方法になっている。


### EEPROM の書き込み

{% capture text %}


## EEPROM

If this is the first time you're flashing the boards, you have to flash EEPROM

0. If your keyboard is plugged in, unplug it
1. Open a terminal, and navigate to the qmk_firmware folder
2. Run `ls /dev | grep tty` Note down which ports you see
3. Plug the keyboard in, if it's new, it should enter bootloader, 
    if it's not new, see **Entering bootloader** on how to enter bootloader mode
4. Right after entering bootloader, run `ls /dev | grep` tty again. 
    There should be a new tty, this is the bootloader TTY, note it down. 
    If nothing shows see Entering bootloader on how to enter bootloader mode
5. For the left hand side, run 
    `avrdude -c avr109 -p m32u4 -P /dev/ttyS1 -U eeprom:w:"./quantum/split_common/eeprom-lefthand.eep":a` 
    Replace **/dev/ttyS1** with the port you noted down earlier. 
    If you're on windows using msys2, replace **/dev/ttyS1** with COM2, 
    note that the number is one higher than the tty number.
6. Do the same For the right hand, but change the file to **eeprom-righthand.eep**

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/keyboards/vitamins_included at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/tree/master/keyboards/vitamins_included)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

最初にこれをやらねばならないらしいので実行する。



#### 左側の EEPROM を書き込む

```sh
$ avrdude -c avr109 -p m32u4 -P /dev/tty.usbmodem1451 -U eeprom:w:"./quantum/split_common/eeprom-lefthand.eep":a

Connecting to programmer: .
Found programmer: Id = "CATERIN"; type = S
    Software Version = 1.0; No Hardware Version given.
Programmer supports auto addr increment.
Programmer supports buffered memory access with buffersize=128 bytes.

Programmer supports the following devices:
    Device code: 0x44

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.00s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading input file "./quantum/split_common/eeprom-lefthand.eep"
avrdude: input file ./quantum/split_common/eeprom-lefthand.eep auto detected as Intel Hex
avrdude: writing eeprom (15 bytes):

Writing | ################################################## | 100% 0.05s

avrdude: 15 bytes of eeprom written
avrdude: verifying eeprom memory against ./quantum/split_common/eeprom-lefthand.eep:
avrdude: load data eeprom data from input file ./quantum/split_common/eeprom-lefthand.eep:
avrdude: input file ./quantum/split_common/eeprom-lefthand.eep auto detected as Intel Hex
avrdude: input file ./quantum/split_common/eeprom-lefthand.eep contains 15 bytes
avrdude: reading on-chip eeprom data:

Reading | ################################################## | 100% 0.01s

avrdude: verifying ...
avrdude: 15 bytes of eeprom verified

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

`/dev/tty.usbmodem1451` は人によって変化するので `ls /dev/tty*` などで確認する。

#### 右側の EEPROM を書き込む

```sh
$ avrdude -c avr109 -p m32u4 -P /dev/tty.usbmodem1451 -U eeprom:w:"./quantum/split_common/eeprom-righthand.eep":a

Connecting to programmer: .
Found programmer: Id = "CATERIN"; type = S
    Software Version = 1.0; No Hardware Version given.
Programmer supports auto addr increment.
Programmer supports buffered memory access with buffersize=128 bytes.

Programmer supports the following devices:
    Device code: 0x44

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.00s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: reading input file "./quantum/split_common/eeprom-righthand.eep"
avrdude: input file ./quantum/split_common/eeprom-righthand.eep auto detected as Intel Hex
avrdude: writing eeprom (15 bytes):

Writing | ################################################## | 100% 0.05s

avrdude: 15 bytes of eeprom written
avrdude: verifying eeprom memory against ./quantum/split_common/eeprom-righthand.eep:
avrdude: load data eeprom data from input file ./quantum/split_common/eeprom-righthand.eep:
avrdude: input file ./quantum/split_common/eeprom-righthand.eep auto detected as Intel Hex
avrdude: input file ./quantum/split_common/eeprom-righthand.eep contains 15 bytes
avrdude: reading on-chip eeprom data:

Reading | ################################################## | 100% 0.01s

avrdude: verifying ...
avrdude: 15 bytes of eeprom verified

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

`eeprom-righthand.eep` に変更してこちらも書き込み完了。



### default のファームを書き込む

{% capture text %}


## Flashing

If you haven't flashed EEPROM before, do that first.

To flash keymaps onto the keyboard, use:

```sh
make lets_split_vitamins/rev1:[KEYMAP]:avrdude
```

from the qmk_firmware folder. Default being the default keymap.

You can plug either half into USB and it will work. you can also remove the TRS/TRRS cable, and plug both halves in. (which is why the default layout has reset on both halves)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/keyboards/vitamins_included at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/tree/master/keyboards/vitamins_included)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


双方ともに上記のコマンドでファーム書き込みをすれば動くらしい。
とりあえずデフォルトの状態でやってみる。

```sh
$ make vitamins_included/rev1:default:avrdude
Making vitamins_included/rev1 with keymap default and target avrdude

avr-gcc (GCC) 8.1.0
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Size before:
   text	   data	    bss	    dec	    hex	filename
      0	  28046	      0	  28046	   6d8e	.build/vitamins_included_rev1_default.hex

Compiling: tmk_core/common/command.c                                                                [OK]
Linking: .build/vitamins_included_rev1_default.elf                                                  [OK]
Creating load file for flashing: .build/vitamins_included_rev1_default.hex                          [OK]
Checking file size of vitamins_included_rev1_default.hex                                            [OK]
 * File size is fine - 28046/28672 (626 free)
Copying vitamins_included_rev1_default.hex to qmk_firmware folder                                   [OK]
Detecting USB port, reset your controller now..................
Detected controller on USB port at /dev/tty.usbmodem1451

Connecting to programmer: .
Found programmer: Id = "CATERIN"; type = S
    Software Version = 1.0; No Hardware Version given.
Programmer supports auto addr increment.
Programmer supports buffered memory access with buffersize=128 bytes.

Programmer supports the following devices:
    Device code: 0x44

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.00s

avrdude: Device signature = 0x1e9587 (probably m32u4)
avrdude: NOTE: "flash" memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file ".build/vitamins_included_rev1_default.hex"
avrdude: input file .build/vitamins_included_rev1_default.hex auto detected as Intel Hex
avrdude: writing flash (28046 bytes):

Writing | ################################################## | 100% 2.19s

avrdude: 28046 bytes of flash written
avrdude: verifying flash memory against .build/vitamins_included_rev1_default.hex:
avrdude: load data flash data from input file .build/vitamins_included_rev1_default.hex:
avrdude: input file .build/vitamins_included_rev1_default.hex auto detected as Intel Hex
avrdude: input file .build/vitamins_included_rev1_default.hex contains 28046 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 0.25s

avrdude: verifying ...
avrdude: 28046 bytes of flash verified

avrdude: safemode: Fuses OK (E:CB, H:D8, L:FF)

avrdude done.  Thank you.
```

```sh
Detecting USB port, reset your controller now..................
```

途中このタイミングで以下のことを行わねばならない。

{% capture text %}

## Entering bootloader

If the keyboard isn't new, and has been flashed before, you need to enter bootloader. 
To enter bootloader, either use the assigned keys on the keymap, 
or if none have been put in the keymap, quickly short the reset to gnd twice. 
(Bottom pins of programming header, see image)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/keyboards/vitamins_included at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/tree/master/keyboards/vitamins_included)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

下の画像の部分を2回すばやくショートさせる。
{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/write-vitamins-avrdude/01_short.jpg %}{% endcapture %}
{% capture caption %}

いつもピンセットで挟んでやってる

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ショートさせるのが成功すると認識されて書き込みが行われる。
既に書き込まれてキーボードになっている場合、これを行わないと EEPROM なども書き込みできない。
ショートさせてから8秒間は書き込みができる。


## 動作確認

実際に動作させてみて動くようなら成功。
書き込んで動くようになるところまで到達した。
