---
layout : post
title  : "poly~機能まとめ"
date   : 2017/06/13
lastchange : 2017-06-13 15:19:44.
tags   :
  - Max
  - Max for Live
  - parallel
  - multi
  - core
  - thread
  - poly~
---

## `poly~`オブジェクトについてのまとめ

1. できることがいまいちわかりにくく
2. 使い所が少なくて地味
3. 割に合わないほどめんどくさい
4. だけど強力な縁の下の力持ち

な`poly~`オブジェクトの機能について使ったことあるものだけまとめる。

## `poly~`の機能

> # poly~
> 
> Manage polyphony/DSP for patchers
> 
> ## Description
> 
> Use the `poly~` to encapsulate a patcher inside an object box, 
> to specify the patcher filename and the number of instances you want to load as arguments to the `poly~` object, 
> and to control object processing and routing in the loaded patcher instances.
> 
> ---
> 
> * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

`poly~`はサブパッチを読み込んで実行できるオブジェクト。
普通のサブパッチと異なるのは、複数の実体を持つことができること。

同じサブパッチを1つのオブジェクトで同時に複数実行することができ、メッセージの送受信先を制御することができる。

## 引数

```
poly~ [filename] [inst_number] @parallel [1/0] @threadcount [int] @args [...]
```

`filename`
: > [patcher-name [symbol]]
  > : The first argument must specify the name of a patcher to be loaded which already exists and is in the Max search path.
  > A subpatch window is not automatically opened for editing when a patcher argument is supplied for the `poly~` object.
  > 
  > ---
  > 
  > * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

    `poly~`は必ず外部にサブパッチが必要。呼び出すサブパッチのファイル名。
    同じディレクトリか、パスの通っている場所に置いておく必要がある。

`inst_number`
: > number-of-instances [int]
  > : *Optional*
  >
  >     The number of patcher instances corresponds to the number of available "voices" This number can be any number between 1 and 1023,
  >     and may be dynamically changed by using the voices message.
  > 
  > ---
  > 
  > * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

    実体をいくつ生成するか。範囲は\\( 1 \to 1023 \\)。

`@parallel` アトリビュート
: > parallel [int]
  > : When this attribute is set to enable parallel processing, 
  > the `poly~` object enables the use of multiple threads to run audio processing for all patcher instances. 
  > If disabled `poly~` runs all patcher instances in the audio processing thread. 
  > The DSP chain must be restarted whenever the parallel attribute is changed. 
  > This attribute is disabled it when Max is hosted by the Live application.
  > 
  > Note
  > : At this time, you cannot specify a single subpatcher on a different core. 
  > When enabled, this attribute splits up the number of voices between the number of processors available. 
  > It is primarily intended for patches that use a significant amount of CPU within multiple voices of the same `poly~` object, 
  > and the multithreading overhead is primarily useful for larger signal vector sizes (at least 32 or greater). 
  > Other situations will not benefit. Using the default threadcount (which is equal to the number of physical cores) is best.
  > 
  > ---
  > 
  > * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

    マルチスレッディング有効化。

    \\[ 
    0 \to 無効 \\\\\
    1 \to 有効
    \\]

    切り替えた際はMSPを再起動させる必要あり。
    有効の場合、各実体毎に指定されたスレッド数で割り振る。実体が1つの場合シングルスレッドになる。
    シグナルベクターサイズが大きい方が優位性が上がる。

    ただし、Max for Liveでは有無を言わさずマルチスレッディングが無効になる。

`@threadcount` メッセージ
: > threadcount
  > : *Arguments*
  > 
  > number of threads [int]
  > 
  > The word threadcount, followed by a number, sets the number of threads used to divide `poly~` instances' audio processing. 
  > The default is the number of processor cores available in your computer. 
  > Typically, the number of threads should be set to the number of processor cores in your computer for best performance. 
  > This can also be accomplished by sending the message threadcount 0. 
  > If a `poly~` object has sixteen instances and the threadcount is 4,
  > four of the `poly~` instances will process audio in each of four threads.
  > 
  > ---
  > 
  > * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

    マルチスレッディング時のスレッド数を指定する。
    実体数16、スレッド数4とした場合、1スレッドで4つの実体を処理する。
    最適なスレッド数はコンピュータのコア数と等しい。
    `threadcount 0`がデフォルト値。コア数と等しくなる。
    
    と書いてあるが、コア数の倍ぐらいを指定したほうがパフォーマンス上がることがある。

`@args` アトリビュート
: > args [10 atoms]
  > When using messages to specifie arguments for a `poly~` object's loaded patch,
  > the patch must be reloaded by setting the patchername attribute for new arguments to take effect after initial load.
  > 
  > ---
  > 
  > * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

  サブパッチに渡す引数を指定する。スペース区切りで最大10コまで。
  指定した引数はサブパッチ内の`#1`、`#2`、`#3`...が置き換わる。
  変更したら再読み込み必須。

## inlet, outlet

> ## Output
> 
> message
> : Each in object in a patcher loaded by the `poly~` or `pfft~` objects appears as an inlet at the top of the object. 
> Messages received at the first message inlet of the `poly~` or `pfft~` object are sent to the first in object
> (i.e., the in 1 object) in the loaded patcher, and so on. 
> The number of total inlets in a `poly~` or `pfft~` object is determined by whether 
> there are a greater number of `in~` or `in` objects in the loaded patch 
> (e.g., if your loaded `poly~` patcher has three `in~` objects and only two `in` objects,
> the `poly~` object will have three inlets, two of which will accept both signals and anything else, 
> and a third inlet which only takes signal input).
> 
> ---
> 
> * [in Reference](https://docs.cycling74.com/max7/maxobject/in)

> ## Output
> 
> (patcher)
> : Any messages received by an `out` object in a loaded patcher appear at the signal outlet of the `poly~` or `pfft~` object 
> which corresponds to the number argument of the `out` object. 
> The signal outputs in a `poly~` or `pfft~` object are a mix of the outputs of all instances of the patcher's outputs 
> which correspond to that number.
> 
> ---
> 
> * [out Reference](https://docs.cycling74.com/max7/maxobject/out)

`poly~`オブジェクトのメッセージデータの入出力には`in`、`out`オブジェクトを用いる。
シグナルデータの入出力には`in~`、`out~`を用いる。
それぞれ引数が必須で、左からいくつめのinlet/outletかを指定する。

同じoutletからシグナルとメッセージを混在させて出力する訳にはいかないので、
outletの数は`out`と`out~`の最大値を合算したものになる。

inletの場合は混在できるので、`in`/`in~`の最大値がinletの個数となる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/01-inout.png %}{% endcapture %}
{% assign caption = 'inlet/outletサンプル' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## メッセージ送信制御

> target [int]
> : The `poly~` instance that will receive subsequent messages 
> (other than messages specifically used by the `poly~` object itself) 
> arriving at the `poly~` object's inlets - for example, The message `target 2` routes messages to the second instance. 
> If the target message specifies a value greater than the current number of instances (copies) of the loaded patcher,
> the message will be sent to the highest numbered instance 
> (e.g., sending the message `target 2` to a `poly~` object containing only a single instance 
> will send subsequent messages to the first instance). 
> The message `target 0` sends input to all instances, 
> and using any negative number value with the target message will disable input to all instances.
> 
> ---
> 
> * [Max 7 - poly~ Reference](https://docs.cycling74.com/max7/maxobject/poly~)

どの実体にメッセージを送るのかを`target [int]`メッセージで制御する。
`target 0`の場合、全ての実体にメッセージを送信する。

実体の総数より大きなターゲットを指定した場合、一番最後の実体にメッセージが送られる。
負数を指定した場合、どこにもメッセージは送られない。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/02-target.png %}{% endcapture %}
{% assign caption = 'targetサンプル' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

メッセージは送信先を指定することができるが、シグナルは送信先を指定することができない。
出力も全ての実体の出力が同じアウトレットから出力されることになる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/03-out.png %}{% endcapture %}
{% assign caption = 'シグナルは合算され、メッセージは最後に受け取ったメッセージが表示されている' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## `thispoly~`オブジェクト

> bang
> : Reports the instance number of the patch. The first instance is reported as 1 .
> 
> --- 
> 
> mute
> : *Arguments* 
> 
>   mute-flag [int]
> 
>   Disables DSP processing for the loaded instance of the patcher when the message `mute 1` is received. 
>   This message can be combined with an int message which toggles the "busy" state of the patcher 
>   to create voices in a patcher which are only on while they play a "note".
>
> ---
>
> ### Output
> 
> Out left outlet
> : The instance number, starting at 1, reported when `thispoly~` receives the bang message. 
> If the patcher containing `thispoly~` was not loaded within a `poly~` object, 0 is output.
> 
> Out right outlet
> : If the loaded instance of the patcher is muted, a 1 is output. 
> If the instance is not muted, a 0 is output. 
>
> ---
> 
> * [thispoly~ Reference](https://docs.cycling74.com/max7/maxobject/thispoly~)

*bang* を与えると左のアウトレットから実体の番号を出力してくれる。 
*mute* に続き1/0を与えることでその実体でのシグナル処理の無効/有効を切り替えることができる。
シグナル処理が無効の場合、右アウトレットから`1`が出力される。

`poly~`にメッセージを与えることでもシグナル処理の有効/無効の切り替えは可能。`mute 3 1`で3番目の実体でのシグナル処理が停止する。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/04-thispoly.png %}{% endcapture %}
{% assign caption = '
1. シグナルが有効
2. 外部からシグナル処理を停止
3. 内部からシグナル処理を停止
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## `#0`トリック

`poly~`パッチ内の`#0`には実体毎に固有のIDが振られる。
`poly~`パッチ内で`send`/`receive`を通常と同じように利用すると全ての実体にメッセージ/シグナルが送受信されてしまうが、
`#0`を利用することで、実体毎に異なるメッセージ/シグナルを送受信することができる。

名前の先頭に`#0`ある場合のみ、個別のIDが振られる。`name_#0`など、名前の途中に存在する場合はIDが割り当てられない。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/05-ID.png %}{% endcapture %}
{% assign caption = '実体同士でのメッセージ/シグナル共有、あるいは実体個別でのメッセージ/シグナル共有' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 引数による名前空間(プレフィックス)トリック

`#0`の応用。引数で`poly~`固有の名前を与え、それを`send`/`receive`の一部に利用することで
他のパッチの`send`/`receive`と名前が被ってしまわないようにする。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-summary/06-prefix.png %}{% endcapture %}
{% assign caption = '名前空間として引数を利用する' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

