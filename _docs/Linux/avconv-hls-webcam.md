---
layout : post
title  : avconvとWebカメラでHLS(HTTP Live Streaming)配信を行う
date : 2017/10/23
lastchange : 2017-10-23 22:09:51.
tags   :
  - avconv
  - alsa
  - Webcam
  - video4linux2
  - hls
  - http live streaming
---

## Webカメラの映像をライブストリーミングする

方法をある程度絞ることができたのでまとめる。

参考:
* [Capture/Webcam – FFmpeg](https://trac.ffmpeg.org/wiki/Capture/Webcam)
* [ffmpeg open webcam using YUYV but i want MJPEG - Super User](https://superuser.com/a/495685)
* [連番静止画からタイムラプス動画を作る - Qiita](https://qiita.com/riocampos/items/2f4fe927b5cf99aff767#-pix_fmt-yuv420p)
* [Linux: 利用できるWebカメラの情報を取得する]({{site.github.url}}{% link _docs/Linux/webcam-usage.md %})
* [Capture/ALSA – FFmpeg](https://trac.ffmpeg.org/wiki/Capture/ALSA)
* [Linux: 利用できるオーディオデバイスの情報を取得する]({{site.github.url}}{% link _docs/Linux/alsa-usage.md %})
* [ffmpegでH.264のプリセットのベンチマーク - Qiita](https://qiita.com/m-sakano/items/63930830faff72a256ac)
* [avconvの使い方]({{site.github.url}}{% link _docs/Linux/avconv-usage.md %})。
* [AACとは (エーエーシーとは) \[単語記事\] - ニコニコ大百科](http://dic.nicovideo.jp/a/aac)
* [【ffmpeg】 fdk-aac を ffmpeg に組み込む : ニコニコ動画研究所](http://looooooooop.blog35.fc2.com/blog-entry-968.html)
* [ライブ動画配信プロトコル（HTTP Live Streaming, HLS）の概要図解メモ（AbemaTV/FRESH!）](http://did2memo.net/2017/02/20/http-live-streaming/)
* [Libav documentation : avconv ](https://libav.org/avconv.html#hls-2)
* [terminal - BMDCapture (bmdtools) capture with Ultrastudio Mini recorder results is color problems - Stack Overflow](https://stackoverflow.com/a/29173771)
 


最終的な引数等はこうなった

```sh
avconv -f video4linux2 -input_format mjpeg -video_size 1280x720 -pix_fmt yvyu422 -i /dev/video0 \
-f alsa -ac 2 -ar 44100 -i hw:1,0 \
-vcodec libx264 -preset veryfast -b:v 512000 \
-acodec libfdk_aac -b:a 256000 -ar 44100 \
-f hls -hls_list_size 10 -hls_time 10 out.m3u8
```


## まずは`avconv`の使い方

こちらにまとめた
[avconvの使い方]({{site.github.url}}{% link _docs/Linux/avconv-usage.md %})



## Webカメラから、必要なフォーマットで映像を取得する

Webカメラからフレームレート、解像度、フォーマットを指定して取得するには
`video4linux2`以降から入力デバイスを指定するまでの間に、

`-framerate`
: フレームレート

`-input_format`
: 入力フォーマット

`-video_size`
: 解像度。形式は`width:height`

を指定する。






{% capture text %}
### Encoding example

Example to encode video from `/dev/video0`:

```sh
ffmpeg -f v4l2 -framerate 25 -video_size 640x480 -i /dev/video0 output.mkv
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Capture/Webcam – FFmpeg](https://trac.ffmpeg.org/wiki/Capture/Webcam)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}
...
This webcam from my example can support both a raw and compressed format, 
and you can tell ffmpeg which one you want with the `-input_format` input option. 
This example will copy the video stream (no re-encoding):

```sh
ffmpeg -f video4linux2 -input_format mjpeg -i /dev/video0 -c:v copy output.mkv
```
....
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [LordNeckbeard](https://superuser.com/users/110524/lordneckbeard) / 
[ffmpeg open webcam using YUYV but i want MJPEG - Super User](https://superuser.com/a/495685)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}







さらに、Webカメラの入力フォーマットYUV4:2:2だが、
これを直接扱う、または`avconv`がYUV4:2:0と認識して変換を行ってしまうと表示がされない問題が生じた。




{% capture text %}
...
As for your command I recommend adding `-pix_fmt yuv420p` as an output option. 
Otherwise ffmpeg may select 4:2:2 as shown in your output and 
I don't know any non-FFmpeg based players that can handle that.
...
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[LordNeckbeard](https://superuser.com/users/110524/lordneckbeard) / 
[ffmpeg open webcam using YUYV but i want MJPEG - Super User](https://superuser.com/a/495685)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}
## `-pix_fmt yuv420p`

画像形式を YUV 形式で YUV420 に指定。jpeg をそのまま動画にすると YUV422 になり、
Mac の QuickTime で再生できなかった。一般的な変換である YUV420 に変換した。
`-vf format=yuv420p` という書式もある。どちらが好ましいのか分からなかった。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[連番静止画からタイムラプス動画を作る - Qiita](https://qiita.com/riocampos/items/2f4fe927b5cf99aff767#-pix_fmt-yuv420p)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


この問題に関しては入力側に`-pix_fmt yvyu422`を指定することで解決した。おそらくHLSに変換する過程で
yuyv422 -> yuv420p 変換が自動的に行われている。

利用できるカメラの情報を取得する方法は
[Linux: 利用できるWebカメラの情報を取得する]({{site.github.url}}{% link _docs/Linux/webcam-usage.md %})にまとめた。

ここまでの指定が

```sh
avconv -f video4linux2 -input_format mjpeg -video_size 1280x720 -pix_fmt yvyu422 -i /dev/video0
```

になる。




## 適切なデバイスから音声を取得する

適切なALSAデバイスからサンプリングレート、チャンネル数を指定して音声を取得するには
`alsa`以降から入力デバイスを指定するまでの間に、

`-ac`
: フレームレート

`-ar`
: 入力フォーマット

を指定する。



{% capture text %}
## Record audio from your microphone

When doing screencast recordings, you usually want to record your voice too:

```sh
ffmpeg -f alsa -ac 1 -ar 44100 -i hw:0 -t 30 out.wav
```

Looking at our example device listing, this would be the same as this:

```sh
ffmpeg -f alsa -ac 1 -ar 44100 -i default:CARD=ICH5 -t 30 out.wav
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Capture/ALSA – FFmpeg](https://trac.ffmpeg.org/wiki/Capture/ALSA)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


利用できるALSAデバイスをの情報を取得する方法は、
[Linux: 利用できるオーディオデバイスの情報を取得する]({{site.github.url}}{% link _docs/Linux/alsa-usage.md %})
にまとめた。


ここまでの指定が

```sh
-f alsa -ac 2 -ar 44100 -i hw:1,0 
```

になる。


## libx264による映像の圧縮方法を指定する

libx264で手軽に圧縮方法などを指定するにはプリセットを用いる。


{% capture text %}
# H.264のプリセット

ffmpegのH.264の説明を読みます。

[https://trac.ffmpeg.org/wiki/Encode/H.264](https://trac.ffmpeg.org/wiki/Encode/H.264)

プリセットはエンコードのスピードと圧縮率に影響をもたらすオプションのコレクションです、と。
遅いプリセットほどより良い圧縮を提供します。そして、速いほうから順に、
ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow, placeboで、
placeboは非常に遅いですが圧縮率もveryslowと1%くらいしか変わらないので無視してください、と書いてあります。
デフォルトは、mediumです。

圧縮は画質に影響するのかという点について、プリセットとは別に画質は`-crf`オプションで指定します。
また`-tune`オプションで最適化されます。今回は`-crf 18`（ほぼ無劣化に見える品質）と
`-tune film`（実写最適化）を指定します。

---

# まとめと考察

veryfastが優秀なので採用。
動画によって（実写、アニメ、ノイズの量など）結果は異なると思うので、試してみるのが大事。
値はマシンスペックに依存する部分もありますが、比率はあまり変わらないと思います。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ffmpegでH.264のプリセットのベンチマーク - Qiita](https://qiita.com/m-sakano/items/63930830faff72a256ac)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ライブ配信はエンコードが実時間より短くなければならない。
なのでまとめにあがっているようにveryfastを採用しようと思う。

また、今回はビットレートでの指定を行う。配信するファイルサイズがわかりやすい方がよい。

ビットレートの指定方法はこちらにまとめた
[avconvの使い方]({{site.github.url}}{% link _docs/Linux/avconv-usage.md %})。


ここまでの指定が

```sh
-vcodec libx264 -preset veryfast -b:v 512000
```

となる。


## libfdk-aacによる音声の圧縮方法を指定する

こちらもまずはプロファイルを指定する。

{% capture text %}
### AAC-LC（AAC Low Complexity）

AACの基本機能のみで構成されたもの。一般的にAACと言えばこのAAC-LCを指す。

MP3と比べて特に低ビットレートにおいて音質が良い。192kbps以上ではMP3との顕著な差はないものの、概ねAACが良質である。
80kbps以下の低ビットレートでは音が破綻することがある。

---

### HE-AAC（High-Efficiency AAC）

通常のAACにSBR（Spectral Band Replication、スペクトル帯域複製）という疑似補完技術を組み込んだもの。

あらかじめ圧縮によって失われる高周波数部分を抜き出し、圧縮後も残る低い周波数の部分との関連を分析し、
SBRという補助的なパラメータとして保存する。低周波数部分はAACとして圧縮される。
再生する際は、中低音域のAACに加えて、圧縮の際にカットされて失われた高音域をSBRデータを元に復元して合成する。

AAC+SBRという形式のため、HE-AACに対応してないデコーダーでも、低音質にはなるもののAAC部分のみを再生できる特長がある。

AAC-LCより高圧縮であり、特に48～80kbps程度の低ビットレートで音の破綻を防ぐのに効果がある。一方でそれ以上はビットレートを上げても音質がそれほど向上しない性質を持つ。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[AACとは (エーエーシーとは) \[単語記事\] - ニコニコ大百科](http://dic.nicovideo.jp/a/aac)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}
#### fdk-aac の使い方

```sh
ffmpeg -i infile -acodec libfdk_aac -profile:a aac_he -ab 48k -ar 44100 -ac 2 -afterburner 1 out.m4a
```

`-profile:a`で指定できるのが

`aac_low`
: AAC-LC 何も指定しないとこれになる

`aac_he`
: HE-AAC 44.1kHzでは28kから使える 推奨32k-48k

`aac_he_v2` 
: HE-AACv2 44.1kHzでは28k-64kまで使える 推奨16-24k（24kHz,22.05kHz）

`aac_ld` 
: AAC-LD flashで再生不可

`aac_eld `
: AAC-ELD flashで再生不可
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【ffmpeg】 fdk-aac を ffmpeg に組み込む : ニコニコ動画研究所](http://looooooooop.blog35.fc2.com/blog-entry-968.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


音質に重点を置いた高ビットレートでの配信ならばHE-AACを使う必要はない。なので今回`-profile:a`の指定はしなかった。

ビットレートやサンプリングレートの指定方法はこちらに
[avconvの使い方]({{site.github.url}}{% link _docs/Linux/avconv-usage.md %})。


ここまでの指定が

```sh
-acodec libfdk_aac -b:a 256000 -ar 44100
```


## HLS(HTTP Live Streaming)の指定




最後にHLSの指定を行う。

{% capture text %}
## 2種類のファイルを用意して動画配信する

HLSでは、 **(1)「.ts」形式の「動画」ファイル** と、 **(2)「.m3u8」形式のプレイリストファイル** という、
２種類のファイルのみでライブ動画配信を実現します。

### .tsファイル(動画)

.tsファイルは、配信映像が数秒の長さに分割された動画ファイルです。
ひとつのライブ配信に対して、大量の.tsファイルが使用されます。


### .m3u8(プレイリスト)

一方、.m3u8ファイルは、ひとつのライブ配信に対してひとつで十分です。

しかし.m3u8は、ライブ配信「全体」のプレイリストではなく、
ライブ配信の「一部」の.tsファイルについてのみのプレイリストとなっています。

どういうことかというと、時間が経過するに従って、
.m3u8ファイルの内容は更新され、プレイリストの内容が少しずつ前進していくのです。

そのため、.m3u8には、「何番目の.tsファイルを起点とするプレイリストか」という情報と
「各.tsファイルのURL・長さ」という情報に加えて、 **「プレイリスト自体の更新頻度(秒)」**
に関する情報が掲載されています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ライブ動画配信プロトコル（HTTP Live Streaming, HLS）の概要図解メモ（AbemaTV/FRESH!）](http://did2memo.net/2017/02/20/http-live-streaming/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

なので個別に.tsファイルを吐き出してプレイリストを更新し続ければ配信は可能だが、
`avconv`は既にHLS用の出力をサポートしている。

HLS出力は拡張子`.m3u8`を見てHLS出力をしてくれるようだが、
一応`-f hls`でHLSフォーマットを指定しておく。

暗号化など、特に指定しない場合は

`-hls_time seconds`
: 1セグメントの時間長、基本は10秒程度

`-hls_list_size size`
: プレイリストにいくつのセグメントが格納できるようにするか指定する。

の2点だけ指定、あるいは既定値のまま実行で問題ない。


{% capture text %}
## 15.4 hls

Apple HTTP Live Streaming muxer that segments MPEG-TS according to the HTTP Live Streaming specification.

It creates a playlist file and numbered segment files. 
The output filename specifies the playlist filename; 
the segment filenames receive the same basename as the playlist,
a sequential number and a `.ts` extension.
 	
```sh
avconv -i in.nut out.m3u8
```

`-hls_time seconds`
: Set the segment length in seconds. 

`-hls_list_size size`
: Set the maximum number of playlist entries. 

....
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Libav documentation : avconv ](https://libav.org/avconv.html#hls-2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}
Got it!

The Mini Recorder shoots at 10 bits rather than 8 
(which I assumed considering Adobe's live encoder said it would be 8).

Here is the fixed code:

```sh
./bmdcapture -m 14 -p yuv10 -C 0 -F nut -f pipe:1 \
| avconv -vsync passthrough -y -i - -vcodec copy -pix_fmt uyvy422 -strict experimental \
-f hls -hls_list_size 999 +live -strict experimental out.m3u8
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Art](https://stackoverflow.com/users/490016/art) /
[terminal - BMDCapture (bmdtools) capture with Ultrastudio Mini recorder results is color problems - Stack Overflow](https://stackoverflow.com/a/29173771)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ここまでの指定が

```sh
-f hls -hls_list_size 10 -hls_time 10 out.m3u8
```

になる。


## ここまでを組み合わせる

ここまでを続けて記述したのが冒頭の

```sh
avconv -f video4linux2 -input_format mjpeg -video_size 1280x720 -pix_fmt yvyu422 -i /dev/video0 \
-f alsa -ac 2 -ar 44100 -i hw:1,0 \
-vcodec libx264 -preset veryfast -b:v 512000 \
-acodec libfdk_aac -b:a 256000 -ar 44100 \
-f hls -hls_list_size 10 -hls_time 10 out.m3u8
```

になる。この引数から必要な部分を変更すればそれに応じたHLS用のファイルが出力されるようになる。

ただ、古い.tsファイルが延々と残り続けるので長時間の配信の場合、適宜削除していかねばならない。


## アクセスする


上記引数を`/var/www/html`で実行した場合、

```url
http://WebサーバのURL/out.m3u8
```

でライブストリーミングを見ることができる。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/avconv-hls-webcam/00_iPhone.PNG %}{% endcapture %}
{% capture caption %}
iPhoneでアクセスして確認したもの。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

