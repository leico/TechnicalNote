---
layout : post
title  : AbletonLiveでCueMixとCueVolumeを実現する
date   : 2017/3/6
lastchange : 2017-06-07 21:09:47.
tags   :
  - Ableton
  - Live
  - 9
  - CueMix
  - CueVolume
---

## Cue機能が貧弱すぎる

Ableton Live 9でDJをする機会があり、その際Cue機能が貧弱すぎて頭にきました。というメモです。

最終的なトラック構成はこちら

{% capture url %}{{site.github.url}}{% link _docs/Live/images/cuemix-cuevolume/01-tracks.png %}{% endcapture %}
{% assign caption = 'トラック構成' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

さらにブロック図も作ってみました。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/cuemix-cuevolume/02-block.png %}{% endcapture %}
{% assign caption = 'diagram' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 実現したい機能

ほしいと思った機能は **Cue Mix** と **Cue Volume** の2種。これらをリターントラックなどで実現します。
なお、それぞれ以下のような機能を想定しています。

Cue
: トラック毎にスイッチが用意されており、
ONにするとプリフェーダーの音がヘッドホンに出力されます。
フェーダーが下がっていれば会場に音は流れません。
DJはこの機能を使って次の曲の選曲や頭出し、ビートマッチングをしているはずです。

CueMix
: マスター出力とCue出力をMixします。
一般的なCue機能に加え、Cueとフロアが混ざった場合のシミュレーションができるようになります。
またヘッドホンだけで練習ができるようになります。なにかと便利。

CueVolume
: Cue出力の音量を調整できる。
オーディオインターフェースのつまみでやるのは使い勝手が悪いので
これもあると便利。

## 実装解説

{% capture url %}{{site.github.url}}{% link _docs/Live/images/cuemix-cuevolume/02-block.png %}{% endcapture %}
{% assign caption = 'diagram' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

を元に解説します。

### トラック1-4の出力部

トラック1-4をDJのDeckとします。各トラックのボリュームは潰さずに利用したいと思います。
デフォルトではそのままMasterに出力されてしまいます。

Masterトラックの出力はリターントラックに送出することができません。
ゆえににデフォルトの状態ではCue Mixを実現できないので、出力先を変更しなければなりません。

Ableton Liveの出力先に指定できるのは他のオーディオトラックorエクスターナル出力なので、
今回は一個余分なオーディオトラック `Track 5` を作成し、そこへDeckの出力を集めます。

### 1-4に基本的なCue機能を追加する

デフォルトではCueは1つしか選択できず使い勝手が悪いです。
そこでリターントラックへの送出を利用して複数のDeckをモニターできる基本的なCue機能を実装します。

ブロック図の `Send D` がそれです。フェーダー前の音を拾いたいので `D CUEMix_Cue` への送出は `Pre` になっています。

### `Track 5` からの出力をCueMixへ送る

`Track 5` は `C Filter` への送出( `Send C` )のみを行っています。`C Filter` には本来であればMasterの出力に入るフィルタ等が入っています。
`Track 5` にMasterの出力に入るフィルタを追加してもよかったですが、オーディオトラックでMasterの調整を行うのはなんか気持ち悪かったので
`C Filter` リターントラックがMasterの代役をつとめることになりました。

`C Filter` の出力は `E CUEMix_Master` と `Master` に送られます。ここの送出量は両方とも0dB固定で動かしません。

このサーキットでMaster出力がCueMixに送られるようになります。

### CueMix

`D CUEMix_Cue` と `E CUEMix_Master` 、CueMix用の出力が実現できたので2つのオーディオシグナルをMixします。
`D CUEMix_Cue` 、 `E CUEMix_Master` 共にMasterに音が出力されてはマズいので `Sends only` になっています。
両トラック共、 `F CUEMix_Volume` へ送出を行っています。 `Send F` は2つのシグナルがただ加算されたものではなく、
Mix具合が反映された状態で送りたいので各々音量が調整された後、 `Post` フェーダーの信号を送っています。

フェーダーは同じOSCメッセージから、真逆の動作をする仕組みになっています。

### CueVolume

バランスが調整された信号が `F CUEMix_Volume` に届けられるので、あとはボリュームで音量を調整するだけです。
`F CUEMix_Volume` の出力は `Ext. Out` になっており、Masterの出力chとは異なる、Cue出力となるchにアサインしています。

### エフェクト類

`A Reverb` 、 `B Delay` にはそれぞれ名称どおりのエフェクトが挿入されています。
両方とも `Post` フェーダーの信号を各Deckから送出することができるようになっています。
エフェクトの信号も直接Masterへ出力されるのではなく、 `C Filter` へ出力されるように、
`Sends only` になっており、 `C send` へ常に出力されるようになっています。

## ここまで書いておいて

各Deckとも `Sends only` にして `C send` を `Post` フェーダーにしてしまえば `Track 5` いらないということに気づいた。
その場合は `A Reverb` と `B Delay` のボリュームを開けておく必要があるけれど。
