---
layout : post
title  : ESP32-dev-moduleのピンマップを確認する。
date   : 2018/05/21
lastchange : 2018-05-21 16:47:09.
tags   :
  - esp32
  - HiLetgo
  - NODE MCU
  - PinMap
  - PinAssign
---

## ピンマップがわからない

Web上で探してもさっぱりだったのでまとめてみた。

参考:

* [Amazon \| HiLetgo ESP32 ESP-32S NodeMCU開発ボード2.4GHz WiFi + Bluetoothデュアルモード](https://amzn.to/2IBBErB)
* [GitHub - espressif/arduino-esp32: Arduino core for the ESP32](https://github.com/espressif/arduino-esp32)


## ESP32 Dev Moduleのピン配置

自分が買ったESP32ボードのピン配置はこのようになっているらしい。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/esp32-pinmap/01_PinAssign.jpg %}{% endcapture %}
{% capture caption %}
ESP32ボードのピン配置
{% endcapture %}
{% assign caption = caption | markdownify %}

{% capture text %}

{% include thumbnail.html url=url caption=caption %}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Amazon \| HiLetgo ESP32 ESP-32S NodeMCU開発ボード2.4GHz WiFi + Bluetoothデュアルモード](https://amzn.to/2IBBErB)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





さらにArduino IDE向けのESP32拡張を作っている github にはこんな図もあった。





{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/esp32-pinmap/02_ESP32_PinMap.png %}{% endcapture %}
{% capture caption %}
ESP32Dev Board PinMap
{% endcapture %}
{% assign caption = caption | markdownify %}

{% capture text %}

{% include thumbnail.html url=url caption=caption %}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[GitHub - espressif/arduino-esp32: Arduino core for the ESP32](https://github.com/espressif/arduino-esp32)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





ここまでは分かったのだけれども、実際に買ったESP32ボードのピン配置が物理的にどうなっているのかは
わからない。
ボードの表にその情報が載っていなくて最初はどうなっているのだとなった。



{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/esp32-pinmap/03_BoardFront.jpg %}{% endcapture %}
{% capture caption %}
ESP32ボードのピン配置
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}





よく見たら裏に印字されていた。





{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/esp32-pinmap/04_BoardPinMap.jpg %}{% endcapture %}
{% capture caption %}
ESP32ボードのピン配置
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


まとめるとこうなる。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/esp32-pinmap/05_ESP32S_Dev_Module.svg %}{% endcapture %}
{% capture caption %}
せっかくSVG作ったので使いたい方は利用してください。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


