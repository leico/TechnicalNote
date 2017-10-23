---
layout : post
title  : avconvの使い方
date : 2017/10/23
lastchange : 2017-10-23 19:54:12.
tags   :
  - avconv
  - alsa
  - bmdtools
---

## avconvの使い方を分かる範囲で

いろいろ試したり資料漁ったりしたログをまとめる。`avconv --help`の出力は最後に。

参考 :

* [trailing options found on command line ffmpeg - Stack Overflow](https://stackoverflow.com/a/38137534)
* [Libav documentation : avconv](https://libav.org/avconv.html)
* [Cut and crop videos with AVconv \| Knolpad](http://www.knolpad.com/cut-and-crop-videos-with-avconv/)
* [Applying multiple filters at once with FFMPEG - Stack Overflow](https://stackoverflow.com/q/6195872)




## 基本的な使い方




{% capture text %}
Basic FFmpeg syntax structure is

```sh
ffmpeg -<options for input 1> -i input 1 -<options for input 2> -i input 2 -<options for output 1> output1 -<options for output 2> output2
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Mulvya](https://stackoverflow.com/users/5726027/mulvya) / 
[trailing options found on command line ffmpeg - Stack Overflow](https://stackoverflow.com/a/38137534)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
```sh
usage: avconv [options] [[infile options] -i infile]... {[outfile options] outfile}...
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
`avconv --help`
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

というように最初にオプションを指定し、最後にインターフェイスを指定する。

```sh
$avconv <options for input> -i input <options for output> output
```

## videoオプション

-vframes \<number\>
: set the number of video frames to record
    
    収録するフレーム数を指定する。60fpsの際`60`と入力すれば1秒だけ録画される。

-r \<rate\>
: set frame rate (Hz value, fraction or abbreviation)
    
    フレームレートを指定する。


-s \<size\>
: set frame size (WxH or abbreviation)
    
    画面の解像度を指定する。一般的な解像度は下方にサンプルを載せている。


-aspect \<aspect\>
: set aspect ratio (4:3, 16:9 or 1.3333, 1.7777)
    
    アスペクト比の設定を行う。

-vn
: disable video
    
    映像を扱わない。

-vcodec \<codec\>
: force video codec ('copy' to copy stream)
    
    映像に用いるコーデックを指定する。`copy`の場合そのままコピーする。
    `libx264`と指定すれば libx264 で h264 圧縮が行われる。

-pass \<n\>
: select the pass number (1 or 2)
    
    圧縮時のパスをシングルかデュアルか指定できる。

-vf \<filter list\>
: video filters
    
    ビデオにかけるフィルターを指定する。
    扱い方は下方に記した。


## audioオプション

-ar \<rate\>
: set audio sampling rate (in Hz)
    
    サンプリングレートの設定。単位は`Hz`。

-ac \<channels\>
: set number of audio channels
    
    チャンネル数の指定。デフォルトは`2`。

-an
: disable audio
    
    音情報を扱わない。

-acodec \<codec\>
: force audio codec ('copy' to copy stream)
    
    変換コーデックを指定する。fdk-aac を利用する場合 `-acodec libfdk_aac` となる。

-af \<filter list\>
: audio filters
    
    音声にフィルターをかける。扱い方は映像と同じ。




## 共通のオプション

-b\[:a\|:v\] \<bitrate\>
: ビットレートの設定を行う`-b:a`で音声の、`-b:v`で映像のビットレート指定になる。

-codec\[:a\|:v1\] \<codec\>
: コーデックの指定を行う。`-codec:a`で音声の、`-codec:v`で映像のコーデック指定になる。



## 一般的な解像度指定

{% capture text %}
sqcif
: 128x96

qcif
: 176x144

cif
: 352x288

4cif
: 704x576

16cif
: 1408x1152

qqvga
: 160x120

qvga
: 320x240

vga
: 640x480

svga
: 800x600

xga
: 1024x768

uxga
: 1600x1200

qxga
: 2048x1536

sxga
: 1280x1024

qsxga
: 2560x2048

hsxga
: 5120x4096

wvga
: 852x480

wxga
: 1366x768

wsxga
: 1600x1024

wuxga
: 1920x1200

woxga
: 2560x1600

wqsxga
: 3200x2048

wquxga
: 3840x2400

whsxga
: 6400x4096

whuxga
: 7680x4800

cga
: 320x200

ega
: 640x350

hd480
: 852x480

hd720
: 1280x720

hd1080
: 1920x1080

2kdci
: 2048x1080

4kdci
: 4096x2160

uhd2160
: 3840x2160

uhd4320
: 7680x4320

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Libav documentation : avconv](https://libav.org/avconv.html)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



## フィルターの種類と使い方


例としてクロップの方法を示す。

{% capture text %}
## Cropping videos:

```sh
avconv -i input.avi -vf crop=720:600:0:360 output.flv
```

This is the command you’d enter to get a cropped video output, the parameters are,

-i
: is input file name, followed by the file name present in the same directory, in this case its, `input.avi`.

-vf
: is used to specify a selection, say a rectangle to be used from the input video to process.

`crop=out_w:out_h:x:y`, this format is to crop the selection from the input. 
`out_w` is the output’s width and `out_h` is the output’s height 
and `x` and `y` are the selection to be cropped. 
So in the example above the cropped video will be _720px X 600 px_ but aligned from top at _360px_. 
The remaining areas before the _0px X 360px_ are removed.

There you have it the cropped video.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Cut and crop videos with AVconv \| Knolpad](http://www.knolpad.com/cut-and-crop-videos-with-avconv/)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

フィルタの例としてクロップ(切り抜き)を行うには

```sh
avconv ... -vf crop=w:h:x:y ...
```

という指定を行う。

w
: 出力したい映像の幅

h
: 出力したい映像の高さ

x
: 切り抜く左上のx座標

y
: 切り抜く左上のy座標


複数のフィルタを指定するにはコンマで区切る。また、フィルタのリストをダブルクォートで囲む。


{% capture text %}
Okay, someone helped me somewhere.

I had to separate filters with commas:

```sh
$ ffmpeg -i input.mpg -vf "movie=watermark.png [logo]; [in][logo] overlay=W-w-10:H-h-10, fade=in:0:20 [out]" output.mpg
```

This will apply fadein to both the watermark and the video.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Parziphal](https://stackoverflow.com/users/638668/parziphal) / 
[Applying multiple filters at once with FFMPEG - Stack Overflow](https://stackoverflow.com/q/6195872)
{% endcapture %}
{% assign source = source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

クロップしてリサイズするには


```sh
$avconv ... -vf "crop=800:450:0:200, scale=w1280:h=720" ...
```

のようになる。

以下利用できるフィルター


```sh
$ avconv -filters
avconv version 12.2, Copyright (c) 2000-2017 the Libav developers
  built on Oct 21 2017 23:09:27 with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.5) 20160609
Filters:
aformat          Convert the input audio to one of the specified formats.
amix             Audio mixing.
anull            Pass the source unchanged to the output.
asetpts          Set PTS for the output audio frame.
asettb           Set timebase for the audio output link.
ashowinfo        Show textual information for each audio frame.
asplit           Pass on the audio input to N audio outputs.
asyncts          Sync audio data to timestamps
atrim            Pick one continuous section from the input, drop the rest.
channelmap       Remap audio channels.
channelsplit     Split audio into per-channel streams
compand          Compress or expand audio dynamic range.
join             Join multiple audio streams into multi-channel output
resample         Audio resampling and conversion.
volume           Change input volume.
anullsrc         Null audio source, never return audio frames.
anullsink        Do absolutely nothing with the input audio.
blackframe       Detect frames that are (almost) black.
boxblur          Blur the input.
copy             Copy the input video unchanged to the output.
crop             Crop the input video to width:height:x:y.
cropdetect       Auto-detect crop size.
delogo           Remove logo from input video.
drawbox          Draw a colored box on the input video.
fade             Fade in/out input video
fieldorder       Set the field order.
format           Convert the input video to one of the specified pixel formats.
fps              Force constant framerate
framepack        Generate a frame packed stereoscopic video.
gradfun          Debands video quickly using gradients.
hflip            Horizontally flip the input video.
hqdn3d           Apply a High Quality 3D Denoiser.
hwdownload       Download a hardware frame to a normal frame
hwupload         Upload a normal frame to a hardware frame
interlace        Convert progressive video into interlaced.
lut              Compute and apply a lookup table to the RGB/YUV input video.
lutrgb           Compute and apply a lookup table to the RGB input video.
lutyuv           Compute and apply a lookup table to the YUV input video.
negate           Negate input video.
noformat         Force libavfilter not to use any of the specified pixel formats for the input to the next filter.
null             Pass the source unchanged to the output.
overlay          Overlay a video source on top of the input.
pad              Pad input image to width:height[:x:y[:color]] (default x and y: 0, default color: black).
pixdesctest      Test pixel format definitions.
scale            Scale the input video to width:height size and/or convert the image format.
select           Select frames to pass in output.
setdar           Set the frame display aspect ratio.
setpts           Set PTS for the output video frame.
setsar           Set the pixel sample aspect ratio.
settb            Set timebase for the video output link.
showinfo         Show textual information for each video frame.
shuffleplanes    Shuffle video planes
split            Pass on the input to N video outputs.
transpose        Transpose input video.
trim             Pick one continuous section from the input, drop the rest.
unsharp          Sharpen or blur the input video.
vflip            Flip the input video vertically.
yadif            Deinterlace the input image
color            Provide an uniformly colored input, syntax is: [color[:size[:rate]]]
movie            Read from a movie source.
nullsrc          Null video source, never return images.
rgbtestsrc       Generate RGB test pattern.
testsrc          Generate test pattern.
nullsink         Do absolutely nothing with the input video.
abuffer          Buffer audio frames, and make them accessible to the filterchain.
buffer           Buffer video frames, and make them accessible to the filterchain.
abuffersink      Buffer audio frames, and make them available to the end of the filter graph.
buffersink       Buffer video frames, and make them available to the end of the filter graph.
afifo            Buffer input frames and send them when they are requested.
fifo             Buffer input images and send them when they are requested.
```



## avconvのヘルプ


より詳細なヘルプは `avconv --help all` で出力される。


```sh
$ avconv --help
avconv version 12.2, Copyright (c) 2000-2017 the Libav developers
  built on Oct 21 2017 23:09:27 with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.5) 20160609
Hyper fast Audio and Video encoder
usage: avconv [options] [[infile options] -i infile]... {[outfile options] outfile}...

Getting help:
    -h      -- print basic options
    -h long -- print more options
    -h full -- print all options (including all format and codec specific options, very long)
    -h type=name -- print all options for the named decoder/encoder/demuxer/muxer/filter
    See man avconv for detailed description of the options.

Print help / information / capabilities:
-L                  show license
-h topic            show help
-? topic            show help
-help topic         show help
--help topic        show help
-version            show version
-formats            show available formats
-codecs             show available codecs
-decoders           show available decoders
-encoders           show available encoders
-bsfs               show available bit stream filters
-protocols          show available protocols
-filters            show available filters
-pix_fmts           show available pixel formats
-sample_fmts        show available audio sample formats
-hwaccels           show available HW acceleration methods

Global options (affect whole program instead of just one file:
-loglevel loglevel  set libav* logging level
-v loglevel         set libav* logging level
-y                  overwrite output files
-n                  never overwrite output files
-stats              print progress report during encoding
-vol volume         change audio volume (256=normal)

Per-file main options:
-f fmt              force format
-c codec            codec name
-codec codec        codec name
-pre preset         preset name
-map_metadata outfile[,metadata]:infile[,metadata]  set metadata information of outfile from infile
-t duration         record or transcode "duration" seconds of audio/video
-fs limit_size      set the limit file size in bytes
-ss time_off        set the start time offset
-metadata string=string  add metadata
-target type        specify target file type ("vcd", "svcd", "dvd", "dv", "dv50", "pal-vcd", "ntsc-svcd", ...)
-frames number      set the number of frames to record
-filter filter_list  set stream filterchain
-filter_script filename  read stream filtergraph description from a file

Video options:
-vframes number     set the number of video frames to record
-r rate             set frame rate (Hz value, fraction or abbreviation)
-s size             set frame size (WxH or abbreviation)
-aspect aspect      set aspect ratio (4:3, 16:9 or 1.3333, 1.7777)
-vn                 disable video
-vcodec codec       force video codec ('copy' to copy stream)
-pass n             select the pass number (1 or 2)
-vf filter list     video filters

Audio options:
-aframes number     set the number of audio frames to record
-aq quality         set audio quality (codec-specific)
-ar rate            set audio sampling rate (in Hz)
-ac channels        set number of audio channels
-an                 disable audio
-acodec codec       force audio codec ('copy' to copy stream)
-vol volume         change audio volume (256=normal)
-af filter list     audio filters

Subtitle options:
-sn                 disable subtitle
-scodec codec       force subtitle codec ('copy' to copy stream)
-stag fourcc/tag    force subtitle tag/fourcc
```
