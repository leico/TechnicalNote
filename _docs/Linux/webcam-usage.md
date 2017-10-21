---
layout : post
title  : "Linux: 利用できるWebカメラの情報を取得する"
date : 2017/10/22
lastchange : 2017-10-22 01:53:44.
tags   :
  - avconv
  - web camera
  - v4l2-ctl
  - video4linux
---

## `avconv`でWebカメラを扱う前に

使える規格とかを調べてたり、認識しているか調べたりしたい。

参考:

* [How to get a list of video capture devices (web cameras) on linux ( ubuntu )? (C/C++) - Stack Overflow](https://stackoverflow.com/a/25211651)
* [Linux/v4l2-ctl - とある技術の私書目録](http://seesaawiki.jp/w/renkin3q/d/Linux/v4l2-ctl)
* [Libav documentation : avconv](https://libav.org/avconv.html#video4linux2)


## `v4l-ctl`を使う

接続され、利用できるWebカメラの一覧が`v4l-ctl`で取得できる。

{% capture text %}
You can use the following bash command:

```sh
v4l2-ctl --list-devices
```

In order to use the above command, you must install package `v4l-utils` before. 
In Ubuntu/Debian you can use the command:

```sh
sudo apt-get install v4l-utils
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Denio Mariz](https://stackoverflow.com/users/3915143/denio-mariz) /
[How to get a list of video capture devices (web cameras) on linux ( ubuntu )? (C/C++) - Stack Overflow](https://stackoverflow.com/a/25211651)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`v4l-utils`をインストールする。

```sh
$ sudo apt-get install v4l-utils
```





## 利用できるWebカメラの一覧を取得する

```sh
$ v4l2-ctl --list-devices
UVC Camera (046d:0821) (usb-0000:00:14.0-2):
	/dev/video1

FaceTime HD Camera (Built-in) (usb-0000:00:1a.0-1.1):
	/dev/video0
```

一覧とデバイスのアドレスが表示される。







## Webカメラの情報やフォーマットを調べる

{% capture text %}
### 用例

_/dev/video0_ のデバイス情報を表示する。

```sh
$ v4l2-ctl -d /dev/video0 --info
```

_/dev/video0_ のデバイスに関する全ての情報を表示する。

```sh
$ v4l2-ctl -d /dev/video0 --all
```

_/dev/video0_ のデバイスがサポートするフォーマットの一覧を表示する。

```sh
$ v4l2-ctl -d /dev/video0 --list-formats
```

_/dev/video0_ のデバイスがサポートするフォーマットと
フレームサイズとフレームレートの組み合わせを表示する。

```
$ v4l2-ctl -d /dev/video0 --list-formats-ext
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Linux/v4l2-ctl - とある技術の私書目録](http://seesaawiki.jp/w/renkin3q/d/Linux/v4l2-ctl)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



カメラの情報を表示する。

```sh
$ v4l2-ctl -d /dev/video0 --all
Driver Info (not using libv4l2):
	Driver name   : uvcvideo
	Card type     : FaceTime HD Camera (Built-in)
	Bus info      : usb-0000:00:1a.0-1.1
	Driver version: 4.8.17
	Capabilities  : 0x84200001
		Video Capture
		Streaming
		Extended Pix Format
		Device Capabilities
	Device Caps   : 0x04200001
		Video Capture
		Streaming
		Extended Pix Format
Priority: 2
Video input : 0 (Camera 1: ok)
Format Video Capture:
	Width/Height      : 960/540
	Pixel Format      : 'MJPG'
	Field             : None
	Bytes per Line    : 0
	Size Image        : 1036800
	Colorspace        : sRGB
	Transfer Function : Default
	YCbCr Encoding    : Default
	Quantization      : Default
	Flags             : 
Crop Capability Video Capture:
	Bounds      : Left 0, Top 0, Width 960, Height 540
	Default     : Left 0, Top 0, Width 960, Height 540
	Pixel Aspect: 1/1
Selection: crop_default, Left 0, Top 0, Width 960, Height 540
Selection: crop_bounds, Left 0, Top 0, Width 960, Height 540
Streaming Parameters Video Capture:
	Capabilities     : timeperframe
	Frames per second: 25.000 (25/1)
	Read buffers     : 0
                     brightness (int)    : min=-127 max=127 step=1 default=0 value=0
                       contrast (int)    : min=0 max=64 step=1 default=32 value=32
                     saturation (int)    : min=0 max=128 step=1 default=64 value=64
                            hue (int)    : min=-4500 max=4500 step=100 default=0 value=0
 white_balance_temperature_auto (bool)   : default=1 value=1
                          gamma (int)    : min=100 max=220 step=120 default=220 value=220
           power_line_frequency (menu)   : min=0 max=2 default=3 value=3
      white_balance_temperature (int)    : min=2800 max=6500 step=1 default=6500 value=3863 flags=inactive
                      sharpness (int)    : min=0 max=16 step=1 default=8 value=8
         backlight_compensation (int)    : min=0 max=1 step=1 default=1 value=1
                  exposure_auto (menu)   : min=0 max=3 default=0 value=0
              exposure_absolute (int)    : min=100 max=10000 step=1 default=300 value=300 flags=inactive
                  iris_relative (int)    : min=0 max=0 step=0 default=0 value=0 flags=write-only
```

カメラのフォーマット、サイズ、フレームレートを表示する。

```sh
$ v4l2-ctl -d /dev/video1 --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
	Index       : 0
	Type        : Video Capture
	Pixel Format: 'YUYV'
	Name        : YUYV 4:2:2
		Size: Discrete 640x480
			Interval: Discrete 0.033s (30.000 fps)
			Interval: Discrete 0.042s (24.000 fps)
			Interval: Discrete 0.050s (20.000 fps)
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
...
	Index       : 1
	Type        : Video Capture
	Pixel Format: 'MJPG' (compressed)
	Name        : Motion-JPEG
		Size: Discrete 640x480
			Interval: Discrete 0.017s (60.000 fps)
			Interval: Discrete 0.033s (30.000 fps)
			Interval: Discrete 0.042s (24.000 fps)
			Interval: Discrete 0.050s (20.000 fps)
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
...
		Size: Discrete 1280x720
			Interval: Discrete 0.033s (30.000 fps)
			Interval: Discrete 0.042s (24.000 fps)
			Interval: Discrete 0.050s (20.000 fps)
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
		Size: Discrete 1392x768
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
		Size: Discrete 1504x832
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
		Size: Discrete 1600x896
			Interval: Discrete 0.067s (15.000 fps)
			Interval: Discrete 0.100s (10.000 fps)
			Interval: Discrete 0.133s (7.500 fps)
			Interval: Discrete 0.200s (5.000 fps)
```



## `avconv`で、Webカメラのフォーマット一覧を取得する。

{% capture text %}
Video4Linux2 devices usually support a limited set of widthxheight sizes and framerates.
You can check which are supported using `-list_formats all` for Video4Linux2 devices.

Some usage examples of the video4linux2 devices with avconv and avplay:
 	
```sh
# List supported formats for a video4linux2 device.
avplay -f video4linux2 -list_formats all /dev/video0

# Grab and show the input of a video4linux2 device.
avplay -f video4linux2 -framerate 30 -video_size hd720 /dev/video0

# Grab and record the input of a video4linux2 device, leave the
framerate and size as previously set.
avconv -f video4linux2 -input_format mjpeg -i /dev/video0 out.mpeg
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Libav documentation : avconv](https://libav.org/avconv.html#video4linux2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`avconv`での動作確認前の認識確認にも利用できる。

```sh
$ avconv -f video4linux2 -list_formats all -i /dev/video0
avconv version 12.2, Copyright (c) 2000-2017 the Libav developers
  built on Oct 21 2017 23:09:27 with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.5) 20160609
[video4linux2 @ 0x1cecd60] R :   yuyv422 :           YUYV 4:2:2 : 160x120 176x144 320x240 352x288 640x480 1280x720
[video4linux2 @ 0x1cecd60] C :     mjpeg :          Motion-JPEG : 960x540 1024x576 1280x720
/dev/video0: Immediate exit requested
```

```sh
$ avconv -f video4linux2 -list_formats all -i /dev/video1
avconv version 12.2, Copyright (c) 2000-2017 the Libav developers
  built on Oct 21 2017 23:09:27 with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.5) 20160609
[video4linux2 @ 0x2b3bd60] R :   yuyv422 :           YUYV 4:2:2 : 640x480 160x120 176x144 320x176 320x240 432x240 352x288 544x288 640x360 752x416 800x448 864x480 960x544 1024x576 800x600 1184x656 960x720 1280x720 1392x768 1504x832 1600x896 1280x960 1712x960 1792x1008 1920x1080 1600x1200 2048x1536 2592x1944
[video4linux2 @ 0x2b3bd60] C :     mjpeg :          Motion-JPEG : 640x480 160x120 176x144 320x176 320x240 432x240 352x288 544x288 640x360 752x416 800x448 864x480 960x544 1024x576 800x600 1184x656 960x720 1280x720 1392x768 1504x832 1600x896 1280x960 1712x960 1792x1008 1920x1080 1600x1200 2048x1536 2592x1944
/dev/video1: Immediate exit requested
```
