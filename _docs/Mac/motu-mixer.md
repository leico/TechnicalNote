---
layout : post
title  : MOTU AVB シリーズのディジタルミキサーを使用する
date   : 2018/07/16
lastchange : 2018-07-17 05:13:08.
tags   :
  - MOTU
  - AVB
  - 624
  - 1248
  - 8M
  - 16A
  - 24Ai
  - 24Ao
  - Monitor 8
  - 112D
  - UltraLite AVB
  - UltraLite mk4
  - 8A
  - M64
  - 8D
  - LP32
  - 828es
  - 8pre-es
---

## 正常に動くようになったようなので

買った MOTU 624 が正常動作するようになったようなので 2018-07-16 現在の所、現行の MOTU AVB シリーズの
ディジタルミキサーを使ってみる。

参考:


## ディジタルミキサーを使用する前に



{% capture text %}

## Modeled vintage effects processing

### Vintage processing with 32-bit float precision

Classic reverb. Compression modeled after the legendary LA-2A compressor. 
EQ modeled after British analog console EQs. The 624's mixing and effects DSP engine 
delivers virtually unlimited headroom and the utmost in sound quality.


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MOTU.com - Overview](http://motu.com/products/avb/624)

Internet Archive
: [MOTU.com - Overview](https://web.archive.org/web/20180716105140/http://motu.com/products/avb/624)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





大々的に LA-2A と英国アナログコンソールの EQ をモデリングしましたと書いているが





{% capture text %}
本体を 4x サンプルレート（176.4/192 kHz）で起動した場合、エフェクト機能はオフになります。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[624 ユーザーガイド（日本語版）](https://s3.amazonaws.com/motu-www-data/manuals/avb/624+%E3%83%A6%E3%83%BC%E3%82%B5%E3%82%99%E3%83%BC%E3%82%AB%E3%82%99%E3%82%A4%E3%83%88%E3%82%99%EF%BC%88%E6%97%A5%E6%9C%AC%E8%AA%9E%E7%89%88%EF%BC%89.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

Yes, the mixer effects/DSP are disabled at 4x sample rate (192k). 
This is because all of the DSP has been used up to support a 192k sample rate. 
You have to sacrifice the mixer effects to run the device at such a high rate. 
That's how it works. 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
MOTU Support (2018/04/13 13:37:44 UTC)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




MOTU AVB シリーズに搭載されている Reverb/EQ/Compressor/Gate等のエフェクトは 4x サンプリングレート、
すなわち 176.4/192kHz のサンプリングレートに対応していない。


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/01_Disable192k.png %}{% endcapture %}
{% capture caption %}

176.4/192kHz の場合、 `Effects are disabled at this sampling rate` と言われて有効にできない。
DSP 使用率も最大値になってしまっている。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture text %}

Each one of our interfaces has the same amount of DSP capability. 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
MOTU Support (2018/04/13 21:07:41 UTC)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


この制限は624に限らず全てのMOTU AVB インターフェイスに共通している。
つまり、624 より高額な 1U サイズのMOTU AVB インターフェイスであろうと
192kHz で内蔵エフェクトを有効にできない。



{% capture text %}

すべての入出力チャンネルには、ラージサイズのデジタル・コンソールに匹敵する豪華な機能が搭載されています。
各チャンネルには3 BandのEQと可変ローカット、オートレベル機能、コンプレッサー、エキスパンダー、MS変換、
位相反転スイッチが用意されており、全チャンネルでセンド/リターン・バスを介してリバーブとエコーを利用できます。
しかも、Fireface UCXは192kHz動作時でもこれらの機能を提供します - 
これは、現存する各社のデジタル・コンソールを遥かに凌ぐ性能です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fireface UCX - Synthax Japan Inc. \[シンタックスジャパン\]](https://synthax.jp/fireface-ucx.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




RME TotalMix FX はちゃんと 192kHz でも動作する。





{% capture text %}

### Features
* UAD-2 QUAD or OCTO in an attractive external housing
* Same potent DSP power as UAD-2 PCIe QUAD or OCTO
* 44.1 – 192 kHz DSP Audio Accelerator for Thunderbolt Macs
* Thunderbolt 2 connectivity (Thunderbolt 1 compatible)
* Kensington Security Slot for anti-theft cables
* Fan-free construction for silent operation

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[UAD System Manual (all UAD products except Arrow)](http://media.uaudio.com/support/manuals/UAD_System_Manual_v9.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## High Sample Rates

Some UAD plug-ins do not support sample rates of 192 kHz. 
These plug-ins are listed in the table below. 

| UAD Plug-Ins Unavailable at 192 kHz Sample Rate |
|-------------------------------------------------|
| Chandler GAV19T Amplifier                       |
| ENGL E646 VS Amplifier                          |
| ENGL E765 RT Amplifier                          |
| ENGL Savage 120 Amplifier                       |
| Friedman BE100 Amplifier                        |
| Friedman DS40 Amplifier                         |
| Fuchs Overdrive Supreme 50                      |
| Fuchs Train II Amplifier                        |
| Gallien Krueger 800RB Bass Amp                  |
| Sonnox Oxford Dynamic EQ                        |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[UAD Plug-Ins Manual (all UAD products)](http://media.uaudio.com/support/manuals/UAD_Plug-Ins_Manual_v952.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


いくつか対応していないみたいだがUAD-2プラグインも 192kHz で動作する。

Antelope はどうなんだろう。

ここは頑張ってほしかった・・・と一瞬思ったが、ディジタルミキサーと考えれば


{% capture text %}

Sample rate
: 96kHz / 48kHz

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DiGiCo SD12 : Digital Mixing Console for Live Sound, HoW, Install, Theatre and Broadcast](https://www.digico.biz/docs/products/SD12.shtml)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

* Sampling frequency
    96kHz

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[PRO2C - （MIDAS｜デジタルコンソール）：ベステックオーディオ株式会社](https://www.bestecaudio.com/product/search/index.php/item?cell003=MIDAS&cell004=%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB&label=1&name=PRO2C&id=322)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}

| Sample rates | 96 kHz |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Avid VENUE \| S6L \| Specifications \| Avid](http://www.avid.com/products/venue-s6l-system/specifications)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


車とか家とか買える値段のディジタルミキサーですら (内部でアップサンプリングされていたりはするが) 
96kHz 動作なので妥当、というかコスパ高いのかもしれない。

進めてゆく内に判明してゆくと思うが、MOTU AVB シリーズは Mac が繋がるディジタルミキサーと思ったほうがいい。

ディジタルミキサーとして、個人的にはあと Polarity Inverter と 0.1ms オーダーで最大 10 秒程度の Delay が付いてほしい。
Delay は Dry/Wet とか Feedback とかいらない単純に遅らせるだけのもの。

・・・脇道に逸れたが 96 kHz 以下にして動作させる。
最新のドライバを入れてMOTU AVB インターフェイスをつないだ状態で
右上にあるメニューから _Open Pro Audio Control..._ を実行する。




{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/02_ProAudio.png %}{% endcapture %}
{% capture caption %}

右上のメニュー

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




開いたWebページ左側 _Device_ タブ -> _Configuration_ -> _Sampling Rate_ からサンプリングレートを選択する。



{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/03_Samplingrate.png %}{% endcapture %}
{% capture caption %}

2018-07-16 現在のUI

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## ルーティング概要

ルーティング設定に移る前に、そもそもの概念として MOTU の AVB シリーズではコンピュータの入出力、
オーディオインターフェイスの入出力、内蔵ミキサーの入出力を自由自在に割り当てることが可能となっている。

コンピュータの入出力、オーディオインターフェイスの入出力、内蔵ミキサーの入出力それぞれの間にパッチベイが存在していると考えるとわかりやすい。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/04_Overview.png %}{% endcapture %}
{% capture caption %}
概念図。上側の信号は、下側の好きな場所にアサインすることが可能。
オーディオインターフェイスの入出力とコンピュータの入出力が直接紐付かない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

これらの設定を行うのが Routing タブになる。

Routing タブが複雑なので少しずつ進めてゆく。

まずは画面の説明。初期状態ではこのような画面になっている。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/05_RoutingDefault.png %}{% endcapture %}
{% capture caption %}
初期状態

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

この画面は全てのカテゴリが折り畳まれた状態である。
全てのカテゴリを展開するには一番上、または一番右の▼をクリックする。
カテゴリ毎に展開、折りたたみをする場合はラベル上、または横の▼をクリックする。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/06_Expand.gif %}{% endcapture %}
{% capture caption %}

最大数表示するととんでもない数の入出力が表示される。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


大規模なステージや数十chのインスタレーションとか以外、まずもって必要ない入出力が表示されるので
左側のメニューから表示数を制限する。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/07_Limitation.gif %}{% endcapture %}
{% capture caption %}

各入出力の最大値は

コンピューターへの入力
: 128

コンピュータからの出力
: 128

AVB 入力ストリーム数
: 8 (8ch x 8 streams)

AVB 出力ストリーム数
: 8 (8ch x 8 streams)

ミキサーへの入力数
: 32

ミキサーからの出力
: 
    Group Outs
    : 4 (96kHz) / 6 (44.1kHz)

    Reverb Bus Outs
    : 2

    Aux Outs
    : 6 (96kHz) / 14 (44.1kHz)

    Main Mix
    : 2

    Monitor Outs
    : 2

    Mix Post Fx
    : same as Mixer Inputs

Mix Post Fx はミキサーへ入力された信号に、ミキサーの各種エフェクトがかかった信号が出てくる。
ただしフェーダーの音量は影響しない(プリフェーダー)。


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

画像では各種入出力を8ch にしてAVB Stream を無効にしている。


ここからシグナルのアサイン方法になる。
画面上側が各種入力元を、左側が各種出力先を表している。
コンピュータからの出力 1 を 624 のメイン出力に割り当てる場合、画像の様に
_From Computer1_ と _Main L_ の交点を有効にする。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/08_Routing.png %}{% endcapture %}
{% capture caption %}
上(入力) と 左(出力) の関連付けをマトリクス上で入力してゆく。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

この例にならって好きなアサインを行う。
コンピュータの出力を一旦ミキサーに入れ、ミキサーの出力をオーディオインターフェイスの出力に回したい場合、
このようになる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/09_Mixer.png %}{% endcapture %}
{% capture caption %}
ルーティング例

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ルーティング画面の操作はこれの繰り返しになる。
多いと大変。




## ミキサー概要


AVB ミキサーはほとんど普通のMixerと同じ。
だけれどもMixing タブの初期の画面はこんな感じでそっけない。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/10_Mixing.png %}{% endcapture %}
{% capture caption %}
フェーダーしかない・・・。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ここも左側のメニューで必要なものを表示したり、いらないものを隠したりすることができるようになっている。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/11_Mixer_Effects.gif %}{% endcapture %}
{% capture caption %}
各種エフェクト、入出力の表示/非表示の切り替え


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



ステレオバス(リンク)の作成や、入力のアサイン、プリセットとして保存などを上部のアイコンで行うことができる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/12_Assign_Link.gif %}{% endcapture %}
{% capture caption %}
ミキサー上部に細かい設定がある。


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



エフェクトのON/OFFはエフェクト左上の電源ボタンで行う。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/13_Effects.gif %}{% endcapture %}
{% capture caption %}
左上の DSP パワーを見ていると EQ などはあまりパワーを消費しないが、
Compressor や Reverb は多くの DSP パワーを消費する。

ただし、多くの DSP パワーを消費するのは最初のみで、ユニット数を増やしてもあまり DSP 消費量は変わらない。
また、エフェクトが有効な状態で非表示にしてもエフェクト自体は生きているので消し忘れに注意しなければならない。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


各種ミキサーアウトへの送出は _Aux Send_ / _Group Sends_ の小さなフェーダ、または _Aux Mixing_ で行う。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/14_Outs.png %}{% endcapture %}
{% capture caption %}
_Legends_ に表示されるボタンでプリ/ポスト切り替え、PANの有効/無効、Solo などを指定できる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/15_Aux1.gif %}{% endcapture %}
{% capture caption %}
_Aux Mixing_ では左下のメニューで表示する Output バスを切り替える。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/16_Aux2.gif %}{% endcapture %}
{% capture caption %}
_Sidebar Fader_ -> _Follow 'On Faders'_ から各マスターフェーダーを右端にポップアップさせることができる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Group Outs、Aux Outs のマスターを上げるとそのまま Main Mixに入ってしまうミキサーもあるが、
AVB ミキサーは各出力が独立しており、そのままではGroup/Aux共にMain Mixには干渉しない。

ブロックダイアグラムにするとこのような感じになっている。
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/17_blockdiagram.png %}{% endcapture %}
{% capture caption %}
個々の出力にAux / Group と名前がついているが実質全て Sub out である。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Mixしたい場合はRoutingタブで同じ出力先をアサインする。


一通りの説明を終えて、 96kHz でどの程度まで処理可能かを確認してみる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/motu-mixer/18_16ch.png %}{% endcapture %}
{% capture caption %}
16ch 全部で EQ 、Compressor を有効にしてまだ少し余裕がある。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

自由すぎるルーティングからミキサー運用まで、これだけできれば十分な気がする。

いや、やっぱり極性反転とディレイはほしい。
