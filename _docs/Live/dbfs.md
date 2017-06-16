---
layout : post
title  : Ableton Liveのレベル、Max for Liveでの値
date   : 2017/04/19
lastchange : 2017-06-17 00:24:49.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - Level
---

## Ableton Liveでのレベルの取り扱い

Max for Liveで音量を操作するパラメータはどのように設計するべきか迷ったので、
Ableton Liveでの音量の取り扱い方を聞いてみた。

{% capture text %}
Using \\( 20 \times log_{10}x \\) or \\( 10 \times log_{10}x \\) simply depends on whether the input x is level or power. 
We’re mostly dealing with levels in Live.

Meters and thresholds are in dBFS (with 0 dB meaning digital full scale).
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
Ableton Support
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



この回答をもらうまで完全に *dBFS* の存在を忘れていて、*dBu* っすか？ *dBm* っすか？ って聞いてしまった。
*dBFS* は「ディジタル信号の最大値を、アナログ信号のどのレベルに合わせるか」という場面で利用されているのをよく見る。

ともあれ、*dBFS* はレベル基準の単位なので

\\[
20 \times log_{10} x
\\]

の対数曲線になる。

## Max for Live での *0 dB*

Max for Live で *0 dB* の値を知らねばならないところは3つ

1. トラックボリュームの *0 dB*
2. センドコントロールの *0 dB*
3. Max for Liveからの出力

これらをテストして確認する。

こんなパッチとLiveセットで試してみた。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/dbfs/01_patch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    829.3ocyWsrbZCCEcs4qPiW1gxX4GXn6ZmtLq51NcXD1BPo1RdjjIPyj7sW8
    vlgPCAgsCoajwWjrNmiN5dkdbjm+R1NrvG7EvOAddONxyyDRGvq4cO+RztrB
    jvzMeJ9A1x68Ga+KIdmzDtfrEOQtgHxwaIY31+mVWxpkEXoYvQMQqPxrMD55
    EbblzN4IwASBFCfyCMOBf5GgpVvuZFj86H2WgsiveIht1eLvmPksONzY07Rn
    sSKrInPtuvLX+V3QxMupHzmCS80wdZzHcy39oF43UXdA6gWWFfmWFlllpIdT
    5T8ij4WVE5EmiGPNWwwUXZNnhypvb49Ny8fTqEH8cl7vAj7RvVTQMFT75rN7
    hrNLM1v5njKyZyTos78h9ACH8M69YKEX9VLuqRPTLzYI3MXenKrGNuMn.sEm
    uPESAmEHojSVVKsoD8NHBd9KTdZAQHwzLyGEZhaTugTCUpyFf9WKDXIPxQY+
    V.ffRxNLegM0JXKqntryYXCiLFsv4QNpx8zmAC6hOaUASMUc0IECMNoj.2nn
    oPxgtTg3nRrTo2XJZokXAuE8Ww3kHC.l5jfLj4c9zyffIWcl1jYFkIdpsXKb
    5k0IAYMEUzmsbyFPZmsOq.+rpFQPPmIu0ijldK3dRW3dFqrDqOayIj+GuNkC
    disEwlM6IQyLq215qyOhx5ucFqfws8OXRzXcSrtU0br5bls.Tof7GalQsoxk
    BPI9GMZpZOmkcjL4dv2IhLFOG7MVQ9Ppc2c0ZWhcSRq1M6+Csa5Gf1805bBC
    HYf6T0m5fNZjtjDi6q4X9ez5X56nLdtiIWTuVoXOe001Zxa0XDstRmxaM1wL
    XcxH1AAzfB0wcnmdwSCo0weopJX07rVNYqjn7DG3RNVHITjjvnG0mvWzmMj7
    bL8XGZNQnKtmebLEXw7CGt6eVtGZ3A6J7B5E7Rb.dytF0qWnQcs6KCGX3sCO
    wtfG3sCOPWvy7aGdBbwcCus3A5h9.uI34jkhynOw2L8AF5f9bxh56JdfNl9o
    KKW1JKnpps5Kqa+jFjnpLeu8fEoiMuRn1WM2W1mqtXca+sQPbU4Wop1aM2VR
    a2zXe6PYprvzZRScB0L+zn+BG7U3NA
    -----------end_max5_patcher-----------
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{site.github.url}}{% link _docs/Live/images/dbfs/02_liveset.png %}{% endcapture %}
{% assign caption = '

*Track 2* のボリュームで *1kHz* のサイン波の音量をコントロールできる。
*Track 2* のボリュームを *0 dB* にした時の出力ゲインや
`live.observer`が取得する値を読み取る。

センドコントロールの出力はサイン波の出力が *0 dB* の際、
センドコントロールをどこまで回すと 
*Master* が *0 dB* になるか実験する(*Track 1* の出力は *-inf dB*)。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

---

もう一個忘れていた。`live.gain~`の *raw value* での *0 dB* を求める。
これはMIDIマッピング等で利用する。

{% capture url %}{{site.github.url}}{% link _docs/Live/images/dbfs/03_live.gain.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    450.3ociSsraCBCD7L7UX4yzHHPHs8WopJxDbHaEXGYaPIJp8aud8iPRaRUO
    .FO6rdMyN64zDZi7HWSIuRdijjbNMIwAg.Ig8IzA1ws8LsiFcWuTLNPy7gfV
    Gnr4imJJhfGXJ1.2vUa3BVSOGojGhIGM8bi4zAtunTZFg1vDcTx6AJ1iGDVR
    t5UD.0lS9ChFqxNoZfYPn54D8GuKykWtMls6AQ2FEeqwWzp0urHOirptBWJV
    UhKKKVjSdGy4yzT7U1+TQ5gI9hNFH9Jd0lXJgUAdPzqzrm+KIq3QRlF5DrdT
    3l+BersFqfjg0Tadfdt7tZ0p+PqpVhpS45mwkJ26hxZTqhsF1DucCyXTPyng
    O+kNHZAUC0k9QtbWDNhe8uu0c08Xs6Fp58Rk4exMpd42I1fUdvXOsNew8iyb
    s+56FEDfAr8.mXkOKK2PZzR5hCtxG24xhKAKmKY6uh3myjtFCheqOTKGUai1
    hfghTdoyzx0FPvLfTbEGbPkL291Cssbw0ynsfF8fNaZ9utcrCGl3Jc3Lc2D6
    7vGREtcclaKH7aKcaU7IHx2ivTValw5wFU9t2w5JpOUYKWIFA2.PJV4OS+1S
    0PR0
    -----------end_max5_patcher-----------

*0.921*
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### 結果

トラックボリュームの *0 dB*
: Max for Live では \\(0 dB \fallingdotseq 0.85 \\)

センドコントロールの *0 dB*
: `live.observer`から送られてくる値は\\( 0 \to 1 \\)、
*0 dB* が出力された時のセンドコントロールは最大の時だったため、\\(0 dB = 1 \\)

Max for Liveからの出力
: \\(0 dB = 1 \\)

`live.gain~` *0 dB* の *raw value*
: \\(0 dB \fallingdotseq 0.921 \\)


センドコントロールとMax for Live内部の音量は\\( 0 \to 1 \\)制御で問題無いんだけど、
トラックボリュームがおそらく *+6 dB* まである影響か *0.85* なのがちょっと問題かも。

あと`live.gain~`の *0.921* って・・・。どっちかに統一してほしい。

## 補足:レベルのお話

ディジタル信号は *0 dB* 超えたら即クリップして簡単に音が歪むため、歪みが生じないよう録音側、
出力側共に十分なヘッドマージンを持った値が設定されている。

出力側はだいたい *-20 dBFS* や *-18 dBFS* を *0 VU*、*+4 dBu* に基準が設定されていて、
入力側はだいたい *+18 dBu* や *+24 dBu* が *0 dBFS* に基準が設定されている。

なお、Ableton Liveを使っている上でこれらを意識する必要はあんまりない。
Live から *0 dB* で出してオーディオインターフェイスの音量を調整すれば大体どうにかなる。
オーディオインターフェイスの入力メーターも *0 dB* 入れればだいたい *0 dB* で録れる。

ついでに手が届く価格のオーディオインターフェイスで厳密に対応しているものもあんまりない。

*dBFS* と *dBu* の対応関係もADC,DACに依存するところが大きいし、DAWの種類でも色々異なるらしい。


{% capture text %}
ちなみに、audioeaseのmake a test tone で 0dBFS の波形を作り、各DAWで再生＆ミキサーのメーターを確認してみました。
0dBFSってのはデジタルオーディオの世界でこれ以上大きい音はないって波形ですから、当然0dBFSでメーターが触れるのかと思いきや

DP6
: -2.5dBFS

Logic 8 Pro 
: +1.2dBFS

ProTools 8 LE 
: 0.0dBFS

ProTools は厳格ですなー。って、昔のProToolsは新規ソング作成時にヘッドルームを指定できた気がするんだけど、なくなったのかしら。どこにもなかったよ。
そしてDP6はヘッドルームを多く取っている。Logicは逆、音量を突っ込ませる側に行っている。こうしてみるとおもしろいねー、設計思想が違うとはこのことだな。
どのソフトも0dBFSを越えられる事になっているのは、32bit浮動小数点演算とかをしてるからだと思われマス。（よくわかってない）
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[オーディオインターフェイスのアウトレベルを計測 : レスペス・トランクィル](http://blog.livedoor.jp/qoozy/archives/51840903.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


