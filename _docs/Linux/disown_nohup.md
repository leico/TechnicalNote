---
layout : post
title  : 配信しているコマンドが、 ssh 脱出後でも動作し続けるようにする
date : 2020/04/30
lastchange : 2020-04-30 16:06:56.
tags   :
  - avconv
  - alsa
  - Webcam
  - video4linux2
  - hls
  - http live streaming
  - nohup
---

## ssh を抜けると実行中のコマンドが終了してしまう

ので、ずっと実行し続けるための方法を探った。

参考: 

* [ログアウトしてもコマンド実行し続ける方法_メモ - Qiita](https://qiita.com/MeganLiu/items/29dcfac8aff3a6cec38f)
* [Linuxコマンド(Bash)でバックグラウンド実行する方法のまとめメモ - Qiita](https://qiita.com/inosy22/items/341cfc589494b8211844)
* [HUPシグナルとnohupとdisownとバック/フォアグラウンドジョブの理解 - Qiita](https://qiita.com/yushin/items/732043ee23281f19f983)
* [プロセスを終了するkillコマンドの使い方まとめ！【Linuxコマンド集】](https://eng-entrance.com/linux-command-kill#nohup)
* [psコマンドまとめ - Qiita](https://qiita.com/shell/items/68ed71a7f018e5688f73)






## 端末が終了しても動作し続けるプロセスを生成する

{% capture text %}

ログアウトする（orネットワーク切れる）とき、コマンドを実行し続けたいとき、
以下の方法で、コマンドを実行する。

`$ nohup コマンド &`

> nohupでコマンドを実行した場合は，ログアウトしてもプログラムを実行し続ける。
> 長い処理を行っており，席を外したいときなどに使用すると便利である。
> ログアウトした後は，ジョブではなくプロセスとして管理することになる。
> また，処理中に出力されたメッセージは"nohup.out"に保存される。"
>
> ※参考: [ITpro記事_nohup](https://xtech.nikkei.com/it/article/COLUMN/20060227/230850/?rt=nocnt)


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ログアウトしてもコマンド実行し続ける方法_メモ - Qiita](https://qiita.com/MeganLiu/items/29dcfac8aff3a6cec38f)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## nohupでsshログアウトしても実行維持

nohupコマンドでsshログアウト時に送られるHUPシグナルによる
バックグラウンドジョブのkillを行われないようにする

HUPシグナルについては以下の資料がとても参考になる
参考：  [ http://qiita.com/yushin/items/732043ee23281f19f983 ](http://qiita.com/yushin/items/732043ee23281f19f983)


``` sh
$ nohup sleep 5 &
```

標準出力などがある場合はデフォルトで `./nohup.out` というファイルに出力される。
以下のようにファイルを指定すれば、任意のファイルに出力できる。

``` sh
$ nohup echo test > test.txt &
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Linuxコマンド(Bash)でバックグラウンド実行する方法のまとめメモ - Qiita](https://qiita.com/inosy22/items/341cfc589494b8211844)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`nohup` コマンドが用いるのが一般的なようだ。また、

{% capture text %}


* シェルからバックグラウンドで処理を行うには？
    * `nohup コマンド実行 &` を使う。
    * コマンド実行 => `Ctrl-Z` => `disown` を使う。

定番は前者だが、両者の違いは何なのか？
disownとは一体何をするのか？

---

#### ジョブテーブル

* シェルはセッションに紐づくジョブテーブルを持っている。
    * バックグラウンドジョブを登録している。
    * (フォアグラウンドジョブは未確認。ソースを読むしかないか)
* `jobsビルトインコマンド` で一覧を参照可能。
    * **ここに見える限り、シェルのexit時に `SIGHUP` を送る。**
    * この一覧にない場合、exit時に `SIGHUP` は送らない。
    * **シェルオプションで `nohup` を指定した場合、ジョブテーブルに見えても `SIGHUP` を送らない。**
    * ジョブテーブルから消すには、`disown` を使う。
    * `fg` や `bg` でフォアグラウンド、バックグラウンド化できる。(monitorオプション有効時）

#### `nohup` コマンド

* **`SIGHUP` を無視した状態でプロセスを起動する。**
* シェルは終了時に `SIGHUP` を送るかもしれないが、届いても無視する。 `kill -SIGHUP` で直接送っても無視。
* **`disown` しない限りジョブテーブルに残る。** `fg` , `bg` , `disown` , `Ctrl-Z` などを受け付ける。
* フォアグラウンドジョブに付与した場合でも、 `SIGHUP` を受け付けずに継続する。
* (標準出力/エラー出力が端末の場合に _nohup.out_ に出したりする)
* (尚、 `nohup` を付けて実行すると、`trap` ビルトインコマンドでもキャッチできない)

#### `disown` コマンド

* **ジョブテーブルから外れる。**
    * `fg` 、`bg` コマンドを受け付けず、`jobs` で参照できない。
    * シェルの終了時に `SIGHUP` を送らない。
    * `SIGHUP` を `kill -SIGHUP` などで直接送ると受け付ける。（ `nohup` しない限り）
* (フォアグラウンドジョブ実行状態で自分に `disown` は打てないので、対バックグラウンド専用)

#### シェルのhup/nohupオプション

* **シェルの終了処理でジョブテーブルのジョブに対して `SIGHUP` を送るかどうか。**
    * `disown` でジョブテーブルから外れている場合はこれに関係なく送らない。
* **フォアグラウンドジョブでも `hup` オプションがセットされていれば、シェルの終了時に `SIGHUP` を送る。**
    * 端末が切れた場合にフォアグラウンドジョブにはカーネルからも `SIGHUP` が送られる。上記と合わせて２回のケースがある。
    * 逆に言えば、シェルオプションで `nohup` にしても、フォアグラウンドのジョブ実行中のまま端末を切るとカーネルが `SIGHUP` を送ってくる。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HUPシグナルとnohupとdisownとバック/フォアグラウンドジョブの理解 - Qiita](https://qiita.com/yushin/items/732043ee23281f19f983)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`disown` というコマンドでも終了しないようにできるようだ。両者の違いを簡潔にまとめると、

`nohup`
: `SIGHUP` シグナルを受け付けなくする。
: 端末終了時に送られる `SIGHUP` を無視するので動き続ける。
: それ以外に送られる `SIGHUP` も無視する。

`disown`
: background job に対してのみ指示できるコマンド。
: ログイン中の端末の job 一覧から指定した job を除外する。
: 端末終了時の `SIGHUP` は端末の job に対してのみ送られるので、終了時点の `SIGHUP` を受け取らなくなる。
: なので他から送られる `SIGHUP` は受け付ける。

なので、 `disown` を用いたほうがよさそうな気がするが、
不慮の事故で `SIGHUP` が送られても終了しないという意味では `nohup` のほうがよさそうだ。







## 動き続けるプロセスを終了させる

{% capture text %}

### `-l` オプション

利用できるシグナルの一覧を表示するコマンドだ。実際に見てみよう。

```
$ kill -l
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[プロセスを終了するkillコマンドの使い方まとめ！【Linuxコマンド集】](https://eng-entrance.com/linux-command-kill#nohup)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみると

```sh
$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL      5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

結構多い。

{% capture text %}

このようにたくさんのシグナルが利用できることがわかる。
それぞれのシグナルIDもわかるはずだ。
全部解説しても意味があるとも思えないので、使いやすいものだけご紹介しよう。

| シグナル名 | シグナルID | 動作                                                 |
|------------|------------|------------------------------------------------------|
| HUP        | 1          | ハングアップ（端末が制御不能もしくは切断による終了） |
| INT        | 2          | キーボードからの割り込み（Ctrl+Cと同じ）             |
| KILL       | 9          | 強制終了（デフォルトよりも強制的に修正する）         |
| TERM       | 15         | 終了（デフォルトの設定だ）                           |
| CONT       | 18         | 停止しているプロセスを再開                           |
| STOP       | 19         | 一時停止                                             |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[プロセスを終了するkillコマンドの使い方まとめ！【Linuxコマンド集】](https://eng-entrance.com/linux-command-kill#nohup)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ということなので、

```sh

$ kill -SIGTERM PID
$ kill -SIGHUP PID
$ kill -SIGKILL PID
```

辺りを利用することになるのだろう。これを上から順番に試して消せるものを探すというのが良さそうだ。
ただし `-SIGKILL` は本当に強制終了なのでなるべくは避けたい。






## プロセス番号を確認する

{% capture text %}

## psコマンドとは

実行中のプロセスを表示するコマンドです。表示されるプロセスの要素を下に記します。

| 要素  | 意味                                   |
|-------|----------------------------------------|
| F     | プロセスの状態                         |
| USER  | ユーザID                               |
| PID   | プロセスID                             |
| %CPU  | CPU使用率                              |
| %MEM  | 実メモリ使用量                         |
| VSZ   | 仮想メモリの使用サイズ(キロバイト表示) |
| RSS   | 物理メモリの使用サイズ(キロバイト表示) |
| TTY   | 使用端末                               |
| STAT  | プロセスのステータス                   |
| PRI   | プロセスの優先度                       |
| PPID  | 親プロセスのPID                        |
| NI    | 優先度を示すNICE値                     |
| SZ    | プロセスが確保しているメモリサイズ     |
| WCHAN | スリープ時のカーネル関数名             |

## F(プロセスの状態)

Fは16進数で表記される。

| 状態表記 | 意味                                                     |
|----------|----------------------------------------------------------|
| 00       | 終了                                                     |
| 01       | システム上の常駐プロセス                                 |
| 02       | 親プロセスからトレースされている                         |
| 04       | 親プロセスからトレースされている状態で停止している       |
| 08       | 起動不可能                                               |
| 10       | プロセスがメモリ上にありイベント終了までロックされている |
| 20       | スワップ不可能                                           |


## STAT(プロセスステータス)

| ステータス表記 | 意味                                             |
|----------------|--------------------------------------------------|
| S              | 割り込み可能なスリープ状態                       |
| s              | セッションリーダ                                 |
| D              | 割り込み不可能なスリープ状態                     |
| R              | 実行可能状態(待ち状態)                           |
| O              | 実行中                                           |
| I              | プロセス生成中                                   |
| Z              | 親プロセスとの関係が切れた状態（ゾンビプロセス） |
| N              | 優先度低い                                       |
| >              | 優先度高い                                       |
| +              | フォアグラウンドのプロセスグループに入っている   |
| T              | 停止中                                           |


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[psコマンドまとめ - Qiita](https://qiita.com/shell/items/68ed71a7f018e5688f73)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`ps ax` を実行してみる

```sh
  PID TTY      STAT   TIME COMMAND
....
 6227 ?        S      0:00 sudo nohup ./start_hls.sh
 6228 ?        S      0:00 /bin/sh ./start_hls.sh
 6229 ?        Sl   4000:53 avconv -f video4linux2 -input_format mjpeg -video_size 1 ...
...
```

`6227-9` が HLS 配信を `nohup` で動作したプロセスであることが確認できる。このプロセスは

```sh
$ sudo nohup ./start_hls.sh
```

で生成されたもの。
