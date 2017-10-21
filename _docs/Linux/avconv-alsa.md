---
layout : post
title  : avconvでのALSAデバイス指定方法
date : 2017/10/22
lastchange : 2017-10-22 01:51:56.
tags   :
  - avconv
  - alsa
  - hw
  - device
---

## `hw:0,0`ってなんぞ

`avconv`でALSAデバイスを利用する方法を調べていたら`hw:0,0`という謎の記述を見つけた。


{% capture text %}
# ALSA Input

One alternative is to go via ALSA. 
A similar command to above would be

```sh
ffmpeg -ac 1 -f alsa -i hw:0,0 -acodec libmp3lame -ab 32k -ac 1 -re -f rtp rtp://localhost:1234
```

I'm not sure how this will affect the CPU usage.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Alex Chamberlain](https://raspberrypi.stackexchange.com/users/86/alex-chamberlain) / 
[usb - Live audio stream using FFMPEG - Raspberry Pi Stack Exchange](https://raspberrypi.stackexchange.com/a/1469) 
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


これの意味するのはなんだろうか。






## サウンドカードIDとデバイスID


`hw:X,Y`は

`X`
: サウンドカードID

`Y`
: デバイスID

を示している。


{% capture text %}
`hw:0,0` specifies the default device, on the default sound card. 
To access your second soundcard's first device, you would specify `hw:1,0`.
These are specified in your `.asoundrc`. More on all of this
[here](http://www.alsa-project.org/main/index.php/Asoundrc).
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[John T](https://superuser.com/users/1931/john-t) /
[linux - What do ALSA devices like "hw:0,0" mean? How do I figure out which to use? - Super User](https://superuser.com/a/53962)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}
JohnT's answer gives a good basic. 
I'll follow it up with how to find the devices on your system. 
Use `aplay -l` to get a list of the devices on your system. 
The **hw:X,Y** comes from this mapping of your hardware -- in this case,
X is the **card** number, while Y is the **device** number.

```
$ aplay -l   # note lower-case "L" option
**** List of PLAYBACK Hardware Devices ****
card 0: T71Space [Terratec Aureon 7.1-Space], device 0: ICE1724 [ICE1724]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: T71Space [Terratec Aureon 7.1-Space], device 1: IEC1724 IEC958 [IEC1724 IEC958]
  Subdevices: 0/1
  Subdevice #0: subdevice #0
card 0: T71Space [Terratec Aureon 7.1-Space], device 2: ICE1724 Surrounds [ICE1724 Surround PCM]
  Subdevices: 3/3
  Subdevice #0: subdevice #0
  Subdevice #1: subdevice #1
  Subdevice #2: subdevice #2
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[quack quixote](https://superuser.com/users/12786/quack-quixote) / 
[linux - What do ALSA devices like "hw:0,0" mean? How do I figure out which to use? - Super User](https://superuser.com/a/53977)

{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



認識されているデバイス一覧を調べる方法は

* [Linux: 利用できるオーディオデバイス情報を取得する]({{site.github.url}}{% link _docs/Linux/alsa-usage.md %})

にまとめた。取得した一覧でサウンドカードID,デバイスIDの組み合わせはこのようになる。



```sh
$ arecord -l
**** List of CAPTURE Hardware Devices ****
card 0: PCH [HDA Intel PCH], device 0: CS4206 Analog [CS4206 Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0                                                    //hw:0,0
card 1: U0x46d0x821 [USB Device 0x46d:0x821], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0                                                    //hw:1,0
```

入力デバイスが複数認識されると、サウンドカードに紐づく`Subdevice`の数が増える。
