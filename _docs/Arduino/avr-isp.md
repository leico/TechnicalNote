---
layout : post
title  : Arduino を AVR ライタにする
date   : 2018/08/23
lastchange : 2018-08-27 21:56:47.
tags   :
  - Arduino
  - AVR writer
  - AVRライタ
---

## 自作キーボードが文鎮化したからAVR ライタ側から復活を試みる

その一環のログ

参考:

* [Arduino - Software](https://www.arduino.cc/en/main/software)
* [Arduinoのブートローダの修復方法 \| Physical Computing FAQ& Tutorial](https://physical-computing-lab.net/arduino/learn_arduino_bootloader_install.html)
* [Arduino IDE から、生の AVR にスケッチを書き込む方法（ArduinoIDE 1.6.4以降版。ArduinoをAVR ISPライターとして使う） - nomolkのブログ](http://nomolk.hatenablog.com/entry/2016/06/21/001322)


## Arduino IDE をインストール

[Arduino - Software](https://www.arduino.cc/en/main/software)

ここからダウンロードしてインストールする。
途中寄付を求められるのでお金ある人は寄付をしてダウンロードする。
お金がない人は Just Download する。
お金ある人は Just Download 押さない。

## Arduino を ISP 化する

Arduino を起動した後、以下のように辿ると ISP 化するためのソースコードが開く

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/avr-isp/01_ISP_source.png %}{% endcapture %}
{% capture caption %}

_ファイル -> スケッチ例 -> ArduinoISP -> ArduinoISP_

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




以下ボードの設定

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/avr-isp/02_board.png %}{% endcapture %}
{% capture caption %}

ボードは Arduino Uno を選択

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


書込装置の設定は特に必要ない。

{% capture text %}

Arduino ISPのスケッチアップロード時は書き込み装置の選択は関係無いので
「書き込み装置は AVRISP mkII」は不要ですよ。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
from [Twitter](https://twitter.com/maris_HY/status/1032658939735732224) 
by [まりすさん](https://twitter.com/maris_HY)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

IF you have a programmer, you use one of those options.
If you're simply uploading via a USB cable, you don't need to worry about it.
However, if you've purchased an external programmer, such as the [AVR-ISP](https://www.google.com/search?q=avr-isp),
an [stk500](https://www.google.com/search?q=stk500), 
or even if you're uploading via an [ArduinoISP](https://www.arduino.cc/en/Tutorial/ArduinoISP), 
you'll be selecting one of those options and instead of hitting Upload, 
you'd hit Upload Using Programmer (look under the File menu).

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
from [Why do we need to set Tools > Programmers in Arduino IDE?](http://forum.arduino.cc/index.php?topic=163185.msg1219060#msg1219060)
by KirAsh4
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

外部プログラマーを利用しない限り書込装置の部分は気にしなくていいらしい。まりすさんコメント感謝。


シリアルポートはボードによって異なる。
一度 Arduino IDE を Arduino Uno と接続せずに起動しシリアルポートを確認、
Arduino Uno を接続して Arduino IDE を立ち上げ直して増えた項目が Arduino Uno のシリアルポートになる。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/avr-isp/04_serial.png %}{% endcapture %}
{% capture caption %}

今回は _/dev/cu.usbserial-DA00X4WO_

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

後は書き込むだけで ISP 化する。


## 書き込み後にISPとして利用する方法

### 書込装置の設定

{% capture text %}

また、「ツール」→「書込装置」とたどり、
候補の中から ”Arduino as ISP” を選択します。
これは、Arduinoを書き込み機として使っているということを指定しています。

選択肢の中には、”ArduinoISP” のような紛らわしいものがあります。
間違えずに ”as” の付いているものを選択して下さい。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Arduinoのブートローダの修復方法 \| Physical Computing FAQ& Tutorial](https://physical-computing-lab.net/arduino/learn_arduino_bootloader_install.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

書き込む際は書き込み装置をこのようにする

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/avr-isp/05_as_isp.png %}{% endcapture %}
{% capture caption %}

_ツール -> 書込装置 -> Arduino as ISP_ を選択

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



### 結線方法

{% capture text %}

Pin 10 is used to reset the target microcontroller.

By default, the hardware SPI pins MISO, MOSI and SCK are used to communicate
with the target. On all Arduinos, these pins can be found
on the ICSP/SPI header:

              MISO °. . 5V (!) Avoid this pin on Due, Zero...
              SCK   . . MOSI
                    . . GND

On some Arduinos (Uno,...), pins MOSI, MISO and SCK are the same pins as
digital pin 11, 12 and 13, respectively. That is why many tutorials instruct
you to hook up the target to these pins. If you find this wiring more
practical, have a define USE_OLD_STYLE_WIRING. This will work even when not
using an Uno. (On an Uno this is not needed).

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
ArduinoISP comment
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

| Arduinoのピン | AVRのピン名 | 例）ATmega88/168P/328Pなど | 例）ATtiny2313 |
|---------------|-------------|----------------------------|----------------|
| D10           | RESET       | 1                          | 1              |
| D11           | MOSI        | 17                         | 17             |
| D12           | MISO        | 18                         | 18             |
| D13           | SCK         | 19                         | 19             |
| 5V            | VCC,AVCC    | 7,20                       | 20             |
| GND           | GND         | 8,22                       | 10             |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Arduino IDE から、生の AVR にスケッチを書き込む方法（ArduinoIDE 1.6.4以降版。ArduinoをAVR ISPライターとして使う） - nomolkのブログ](http://nomolk.hatenablog.com/entry/2016/06/21/001322)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


このように結線をする。以下はArduino Uno のAVRポートに接続する例。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/avr-isp/06_ICSP.svg %}{% endcapture %}
{% capture caption %}

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



