---
layout : post
title  : bmdcaptureの使い方
date : 2017/10/20
lastchange : 2017-10-20 16:57:35.
tags   :
  - blackmagic design
  - bmdtools
  - bmdcapture
---

## bmdtoolsの使い方


{% capture text %}
## Usage

```sh
./bmdcapture -C 1 -m 2 -F nut -o strict=experimental:syncpoints=none -f pipe:1 | avconv -vsync passthrough -y -i - <your options here>
```

-C
: select the capture device if more than one is present.

-F
: define the container format, I suggest using nut.

-f
: output file name, any libavformat compatible url is supported.

-m
: specific modeline, resolution+framerate

-o
: pass AVFormat AVOptions (expert)

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[lu-zero/bmdtools: Basic capture and play programs for Blackmagic Design Decklink](https://github.com/lu-zero/bmdtools/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ということなのだが、いまいち使い方がわからないので調べれた範囲でまとめる。

### helpの表示

```sh
$ bmdcapture --help
bmdcapture: invalid option -- '-'
Usage: bmdcapture -m <mode id> [OPTIONS]

    -m <mode id>:
-> UltraStudio Mini Recorder (-C 0 )

Supported video input display modes and pixel formats:
         0:   525i59.94 NTSC       	 720 x 486 	   29.97 FPS
         1:   525p23.98 NTSC       	 720 x 486 	  23.976 FPS
         2:   625i50 PAL           	 720 x 576 	      25 FPS
         3:   525p59.94 NTSC       	 720 x 486 	 59.9401 FPS
         4:   625p50 PAL           	 720 x 576 	      50 FPS
         5:   1080p23.98           	 1920 x 1080 	  23.976 FPS
         6:   1080p24              	 1920 x 1080 	      24 FPS
         7:   1080p25              	 1920 x 1080 	      25 FPS
         8:   1080p29.97           	 1920 x 1080 	   29.97 FPS
         9:   1080p30              	 1920 x 1080 	      30 FPS
        10:   1080i50              	 1920 x 1080 	      25 FPS
        11:   1080i59.94           	 1920 x 1080 	   29.97 FPS
        12:   1080i60              	 1920 x 1080 	      30 FPS
        13:   720p50               	 1280 x 720 	      50 FPS
        14:   720p59.94            	 1280 x 720 	 59.9401 FPS
        15:   720p60               	 1280 x 720 	      60 FPS

    -v                   Be verbose (report each 25 frames)
    -f <filename>        Filename raw video will be written to
    -F <format>          Define the file format to be used
    -c <channels>        Audio Channels (2, 8 or 16 - default is 2)
    -s <depth>           Audio Sample Depth (16 or 32 - default is 16)
    -p <pixel>           PixelFormat (yuv8, yuv10, rgb10)
    -n <frames>          Number of frames to capture (default is unlimited)
    -M <memlimit>        Maximum queue size in GB (default is 1 GB)
    -C <num>             number of card to be used
    -S <serial_device>   data input serial
    -A <audio-in>        Audio input:
                         1: Analog (RCA or XLR)
                         2: Embedded Audio (HDMI/SDI)
                         3: Digital Audio (AES/EBU)
    -V <video-in>        Video input:
                         1: Composite
                         2: Component
                         3: HDMI
                         4: SDI
                         5: Optical SDI
                         6: S-Video
    -o <optionstring>    AVFormat options
    -w                   Embed a wallclock stream
    -d <filler>          When the source is offline draw a black frame or color bars
                         0: black frame
                         1: color bars
Capture video and audio to a file.
Raw video and audio can be sent to a pipe to avconv or vlc e.g.:

    bmdcapture -m 2 -A 1 -V 1 -F nut -f pipe:1

```



{% capture text %}
`bmdcapture: invalid option -- '-'` 
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このように、使い方は変なオプションを設定すると表示される。


### 基本的な使い方




{% capture text %}
`Usage: bmdcapture -m <mode id> [OPTIONS]`
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




`-m` でモードを指定するとキャプチャが開始される。
モードの種類は認識されるデバイスによって異なるかもしれない。
Ultrastudio Mini Recorder を利用している場合、

{% capture text %}
Supported video input display modes and pixel formats:

| Mode | Definition     | Resolution  | FPS     |
| ---: | :------------- | :---------: | :------ |
| 0    | 525i59.94 NTSC | 720 x 486   | 29.97   |
| 1    | 525p23.98 NTSC | 720 x 486   | 23.976  |
| 2    | 625i50 PAL     | 720 x 576   | 25      |
| 3    | 525p59.94 NTSC | 720 x 486   | 59.9401 |
| 4    | 625p50 PAL     | 720 x 576   | 50      |
| 5    | 1080p23.98     | 1920 x 1080 | 23.976  |
| 6    | 1080p24        | 1920 x 1080 | 24      |
| 7    | 1080p25        | 1920 x 1080 | 25      |
| 8    | 1080p29.97     | 1920 x 1080 | 29.97   |
| 9    | 1080p30        | 1920 x 1080 | 30      |
| 10   | 1080i50        | 1920 x 1080 | 25      |
| 11   | 1080i59.94     | 1920 x 1080 | 29.97   |
| 12   | 1080i60        | 1920 x 1080 | 30      |
| 13   | 720p50         | 1280 x 720  | 50      |
| 14   | 720p59.94      | 1280 x 720  | 59.9401 |
| 15   | 720p60         | 1280 x 720  | 60      |

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`-m 9` とすれば 1080p 30 FPS で収録するということになる。
入力されている信号と同じ映像規格(Definition)でなければキャプチャできない。





### 使用するデバイスの選択

{% capture text %}
-C <num>             number of card to be used
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`-C` オプションで利用するデバイスを選択できる。
認識しているデバイスは help の上部に、



{% capture text %}
```sh
    -m <mode id>:
-> UltraStudio Mini Recorder (-C 0 )
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




とデバイス名と共に表示されている。
この場合、 Ultrastudio Mini Recorder を利用する場合、 `-C 0`と指定する。





### キャプチャする入力端子を指定する


{% capture text %}
-A \<audio-in\>
: Audio input:
    1. Analog (RCA or XLR)
    2. Embedded Audio (HDMI/SDI)
    3. Digital Audio (AES/EBU)

-V \<video-in\>
: Video input:
    1. Composite
    2. Component
    3. HDMI
    4. SDI
    5. Optical SDI
    6. S-Video
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


オーディオ/ビデオの入力端子を指定する。
表示の通りで、HDMIケーブルから音声も映像も受け取る場合、`-A 2 -V 3`
のように指定する。



### その他

分かる範囲のもの





{% capture text %}
-c \<channels\>
: Audio Channels (2, 8 or 16 - default is 2)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

音声のチャンネル数を指定する。指定しない場合はステレオになる。`-c 8`で8ch入力になる。



{% capture text %}
-s \<depth\>
: Audio Sample Depth (16 or 32 - default is 16)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

音声のビット深度。指定しない場合16bit。 `-s 32` で32bitになる。





{% capture text %}
-p \<pixel\>
: PixelFormat (yuv8, yuv10, rgb10)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ピクセルフォーマット。デフォルトではyuv8bit。 `-p rgb10` を指定するとRGB10bitになる。






{% capture text %}
-f \<filename\>
: Filename raw video will be written to
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='help' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


入力されるRAWデータを保存するファイル名を出力する。他のプログラムにパイプ(`|`)を用いて渡したい場合(この利用方法が多いはず)、`pipe:1`を指定する。




### 使用例

```sh
bmdcapture -m 9 -C 0 -A 2 -V 3 -f ./video.raw
```

`-C 0`のデバイスからHDMIで1080p 30FPSでキャプチャしたものを、カレントディレクトリの _video.raw_ に書き込む。


```sh
$ bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -o strict=experimental:syncpoints=none -f pipe:1 | avconv -vsync passthrough -y -i - -vcodec libx264 test.avi
```

`-C 0`のデバイスからHDMIで1080p 30FPSでキャプチャしたものを、`strict=experimental`, `syncpoints=none` オプションを付けてavconvに渡している。

avconvはlibx264コーデックを利用して _test.avi_ に書き出している。

