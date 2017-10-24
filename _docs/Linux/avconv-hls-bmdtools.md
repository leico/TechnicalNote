---
layout : post
title  : "bmdtools: bmdcapture から avconv で HLS(HTTP Live Streaming)配信を行う"
date : 2017/10/24
lastchange : 2017-10-24 21:10:27.
tags   :
  - avconv
  - hls
  - blackmagic design
  - bmdtools
  - bmdcapture
  - libx264
  - libfdk_aac
---

## bmdcapture の取得データで配信する

[avconvとWebカメラでHLS(HTTP Live Streaming)配信を行う]({{site.github.url}}{% link _docs/Linux/avconv-hls-webcam.md %})

でやったことを応用して、今度は `bmdtools`を利用してHDMI入力を配信する。
最終的に用いることになるコマンドはこちら

```sh
$ sudo sh -c "bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -c 2 -s 16 \
-o strict=experimental:syncpoints=none -f pipe:1 \
| avconv -f nut -vsync passthrough  -i pipe:0  \
-ac 2 -ar 48000 \
-vf \"crop=640:480:100:100\" \
-vcodec libx264 -preset ultrafast -b:v 512000 \
-acodec libfdk_aac -b:a 256000 \
-f hls -hls_list_size 10 -hls_time 10 ./out.m3u8"
```


元になったコマンドはこの投稿


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

## bmdcaptureからHDMI入力を取得する

```sh
bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -c 2 -s 16 -o strict=experimental:syncpoints=none -f pipe:1
```

0番目のblackmagic製デバイスからHDMIで映像と音声(2ch/16bit)を、1080p30 形式で取得している。
出力は`avconv`に渡すためにpipeを用いている。

詳しくは[bmdtools: bmdcaptureの使い方]({{site.github.url}}{% link _docs/Linux/bmdcapture-usage.md %})
参照。


## avconvの設定

基本的な使い方は
[avconvの使い方]({{site.github.url}}{% link _docs/Linux/avconv-usage.md %})
参照、HLS(HTTP Live Streaming)配信に利用する引数やコーデックに関しては
[avconvとWebカメラでHLS(HTTP Live Streaming)配信を行う]({{site.github.url}}{% link _docs/Linux/avconv-hls-webcam.md %})
を参照のこと。



### 入力設定

```sh
-f nut -vsync passthrough  -i pipe:0 -ac 2 -ar 48000
```

nut形式の入力、受け取り口は`pipe:0`、ステレオ48000kHzのサンプリングレートで取得している。

{% capture text %}
##17.10 pipe

UNIX pipe access protocol.

Allow to read and write from UNIX pipes.

The accepted syntax is:
 	
```sh
pipe:[number]
```

number is the number corresponding to the file descriptor of the pipe (e.g. 0 for stdin, 1 for stdout, 2 for stderr). 
If number is not specified, by default the stdout file descriptor will be used for writing, stdin for reading.

For example to read from stdin with avconv:
 	
```sh
cat test.wav | avconv -i pipe:0
# ...this is the same as...
cat test.wav | avconv -i pipe:
```

For writing to stdout with avconv:
 	
```sh
avconv -i test.wav -f avi pipe:1 | cat > test.avi
# ...this is the same as...
avconv -i test.wav -f avi pipe: | cat > test.avi
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Libav documentation : avconv ](https://libav.org/avconv.html#pipe)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

### 必要部分の切り抜き

```sh
-vf "crop=640:480:100:100"
```

必要な部分だけ、`crop`フィルタを用いて切り抜いている。

### 映像圧縮方法の設定

```sh
-vcodec libx264 -preset ultrafast -b:v 512000
```

x264で圧縮する。プリセットは`ultrfast`、ビットレートは512kbpsとした。


### 音声圧縮方法の設定

```sh
-acodec libfdk_aac -b:a 256000
```

fdk-aacで圧縮する。ビットレートは256kbps



### HLSの設定

```sh
-f hls -hls_list_size 10 -hls_time 10 ./out.m3u8
```

HLSで出力する。プレイリストファイルは`out.m3u8`、
動画ファイルは`out[n].ts`リストには最大10個の動画ファイル、1つの動画ファイルの時間は10秒。


## avconvが`sudo`しても一般ユーザ権限で実行されてしまう

権限の問題で`/var/www/html`にエンコードしたファイルとプレイリストが出力できなかった(書き込み権限がない)。

`avconv`まで含めて管理者権限で実行するために

```sh
sudo sh -c "実行内容"
```

としている。実行内容をダブルクォートで囲む必要があるため、
ビデオフィルタを指定する際のダブルクォートをエスケープしている。


{% capture text %}
#### MANに書いてある．

困って、Manを読んだらManに書いてあった．SudoのMan最高

> _/home_ パーティションにあるディレクトリのディスク使用量リストを作成する。
> `cd` とファイルリダイレクションが動作するように、サブシェルでコマンドを実行している点に注意すること。
> 
> ```sh
> % sudo sh -c "cd /home ; du -s * │ sort -rn > USAGE"
> ```

#### つまりsudoでsh を起動してねってことらしい

```sh
sudo sh -c "do_something > /etc/fstab.d/foo"
```

出来た．
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[sudo コマンド実行しても、リダイレクトでファイルに書き込めない． - それマグで！](http://takuya-1st.hatenablog.jp/entry/20110423/1303586896)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


## 実行結果

最終的なコマンドはこのようになった。

```sh
$ sudo sh -c "bmdcapture -C 0 -m 9 -F nut -V 3 -A 2 -c 2 -s 16 \
-o strict=experimental:syncpoints=none -f pipe:1 \
| avconv -f nut -vsync passthrough  -i pipe:0  \
-ac 2 -ar 48000 \
-vf \"crop=640:480:100:100\" \
-vcodec libx264 -preset ultrafast -b:v 512000 \
-acodec libfdk_aac -b:a 256000 \
-f hls -hls_list_size 10 -hls_time 10 ./out.m3u8"
```

これを _/var/www/html_ で実行し、他のコンピュータからアクセスして表示されるか検証する。

{% capture url %}{{site.github.url}}{% link _docs/Linux/images/avconv-hls-bmdtools/00_iPhone.PNG %}{% endcapture %}
{% capture caption %}
ちゃんと表示されれば成功。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

