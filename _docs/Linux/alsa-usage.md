---
layout : post
title  : "Linux: 利用できるオーディオデバイスの情報を取得する"
date : 2017/10/22
lastchange : 2017-10-22 01:53:03.
tags   :
  - avconv
  - alsa
  - aplay
  - arecord
---

## ALSAで音声入力/音声出力できるデバイスのリストを表示したい

映像だけでなく、音声の入出力デバイスも確認したいのでその方法を調べた。

参考 : 

## 出力デバイス一覧は`aplay`を利用する

`aplay --list-devices`で出力デバイスの一覧を取得できる。


{% capture text %}
## Find the hardware address

### Using aplay

The stock ALSA utility `aplay` can tell you the hardware addresses of the devices it is ready to use. 
Simply supply to aplay the `--list-devices` argument.

```sh
$ aplay --list-devices
**** List of PLAYBACK Hardware Devices ****
card 0: HDMI [HDA ATI HDMI], device 3: ATI HDMI [ATI HDMI]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

Note, for this card the notation for the physical address would be `hw:0,3`. While the device name, is HDMI
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Configuring Sound on Linux/HW Address - Wikibooks, open books for an open world](https://en.wikibooks.org/wiki/Configuring_Sound_on_Linux/HW_Address)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



```sh
$ aplay --list-devices
**** List of PLAYBACK Hardware Devices ****
card 0: PCH [HDA Intel PCH], device 0: CS4206 Analog [CS4206 Analog]
  Subdevices: 0/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 1: CS4206 Digital [CS4206 Digital]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 3: HDMI 0 [HDMI 0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 7: HDMI 1 [HDMI 1]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 8: HDMI 2 [HDMI 2]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```


## 入力デバイス一覧は`arecord`を利用する

`arecord -l`で取得できる。もちろん、`arecord --list-devices`でも可能。

{% capture text %}

## Display all soundcards and digital audio devices

Type the following command:
```sh
$ arecord -l
```

Outputs:

```sh
**** List of CAPTURE Hardware Devices ****
card 0: Intel [HDA Intel], device 0: STAC92xx Analog [STAC92xx Analog]
  Subdevices: 2/2
  Subdevice #0: subdevice #0
  Subdevice #1: subdevice #1
card 1: SAA7134 [SAA7134], device 0: SAA7134 PCM [SAA7134 PCM]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

You need to use card number and device number while playing various media. 
For example, to play a radiostation (FM freq # 98.3) from card #1 and device 0 (hw=.), enter:

```sh
$ mplayer radio://98.3 -radio driver=v4l2:device=/dev/radio0:adevice=hw=1.0
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[arecord: Linux Command to List all Soundcards and Digital Audio Devices – nixCraft](https://www.cyberciti.biz/tips/howto-display-soundcards-digital-audio-devices.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

```sh
$ arecord -l
**** List of CAPTURE Hardware Devices ****
card 0: PCH [HDA Intel PCH], device 0: CS4206 Analog [CS4206 Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: U0x46d0x821 [USB Device 0x46d:0x821], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

