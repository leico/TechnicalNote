---
layout : post
title  : "poly~:実体毎に個別のメッセージ/シグナルを送信する"
date   : 2017/06/13
lastchange : 2017-06-15 13:26:08.
tags   :
  - Max
  - Max for Live
  - parallel
  - multi
  - core
  - thread
  - poly~
---

## `poly~`で同じモジュールを複数使いたい場合に

[`poly~`機能まとめ]({{ site.github.url }}{% link _docs/Max/poly-summary.md %})
で触れたとおり、`target n`メッセージで送信先を毎回変更しなければならないので、
実体毎に異なるメッセージを送るのは少し面倒。

シグナルに関してはそもそも同じ信号しか送れない。
同様に同じアウトレットに全ての実体の出力が合流して出てくるためその後の処理に困る。

なので個々の実体に別々のメッセージ/シグナルを送受信する方法を考える。

## `send`/`receive`を用いる

`in`/`out`、`in~`/`out~`の仕様を曲げることはできないので他の方法を考える。

`send`/`receive`、`send~`/`receive~`を利用するのだが、
どんな名前を、どうやってつけるかという話になる。

## ランダムではないけれど、実体毎に異なるもの

要件を洗い出す:

1. 実体毎に個別
2. 起動するたびにランダムな値では困る
3. 増産する場合を考えるとある程度規則的である方が望ましい

毎回ランダムな数値だと起動するたびに外側の`send`/`receive`の名前を変更する必要がある。
内外で通信するメッセージ/シグナルの数が多ければ多いほど面倒な処理が発生するので`#0`
は使えない。他の方法で実体毎に異なる名前をつけるしかない。

となると`thispoly~`のIDになる。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-individual-signal/01-thispoly.png %}{% endcapture %}
{% assign caption = '`thispoly~`に *bang* を送ると実体のIDを出力する。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## `send`/`receive`の名前を変更する

`thispoly~`から実体ごと異なる番号が出力されるのはいいが、
`send`/`receive`オブジェクトの送受信先(名前)を後から
`thispoly~`の出力で上書きできなければならない。

{% assign text='
set
: *Arguments*

  name [symbol]

  The word set, followed by a symbol, provides a name for receive, 
  as if that name had been typed in as an argument.
' | markdownify %}
{% assign source='
[r Reference](https://docs.cycling74.com/max7/maxobject/receive)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% assign text='
set
: *Arguments*

  object-name [symbol]

  The word set, followed by a symbol, changes the name of the `receive~` so that 
  it connects to different `send~` objects that have the symbol as a name. 
  If no `send~` objects exist with the name, the output of `receive~` is 0.
' | markdownify %}
{% assign source='
[receive~ Reference](https://docs.cycling74.com/max7/maxobject/receive~)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% assign text='
set
: *Arguments*

  object-name [symbol]

  The word set, followed by a symbol, changes the name of the `send~` so that 
  it connects to different `receive~` objects that have the symbol as a name. 
  (If no `receive~` objects with the same name exist, `send~` does nothing.)
' | markdownify %}
{% assign source='
[send~ Reference](https://docs.cycling74.com/max7/maxobject/send~)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

 

`receive`、`receive~`、`send~`は`set name`メッセージであとから受信元/送信先を変更できる。
でも`send`は不可能。


{% assign text='
# forward

Send messages to specified receive objects

## Description

Relays messages to other objects remotely. 
Unlike the `send` object, the destination `receive` object of forward 
can be changed with each message.

---

send
: *Arguments*a

  arguments [list]

  The word send, followed by the name of a `receive` object, 
  sets the destination for any subsequent messages received by the forward object. 
  This ability to change the destination of messages on the fly distinguishes 
  forward from the `send` object.
' | markdownify %}
{% assign source='
[forward Reference](https://docs.cycling74.com/max7/maxobject/forward)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`forward`オブジェクトはメッセージ毎に送信先の`receive`オブジェクトを変更することができる。
`send name`メッセージで`receive name`オブジェクトに送信先を切り替えることができる。

## 名前を作る

送受信先を後からメッセージで変更できるオブジェクトが見つかったが、
IDだけでは名前にしても1つのメッセージしか送受信することができない。

IDと別の文字列を組み合わせる必要がある。`sprintf`を利用する。

{% assign text='
# sprintf

Format a message of words and numbers

## Description

Uses the common C-language "printf" function inside Max. 
You can combine symbols, organize lists of numbers, or format messages or menu items. 
For complete documentation, refer to a standard C library reference manual.
' | markdownify %}
{% assign source='
[sprintf Reference](https://docs.cycling74.com/max7/maxobject/sprintf)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`sprintf`はいろんなメッセージを指定した書式に合わせて組み合わせて出力する。
これを用いて一意な名前を作る。

## `loadbang`,`thispoly~`,`sprintf`,`forward`,`receive`

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-individual-signal/02-individual.png %}{% endcapture %}
{% assign caption = '
これが実装例

    ----------begin_max5_patcher----------
    452.3ocuUt1SCBCEF9yvuhlZ7anokaay+JFCoCpytvZIskIKF82tvof2kgJt
    uPy4zKumm1WZev2CuV0vMXzUnqQddO364Ao5R30G6g2wZxKYFXXXI+d05s3.
    WWVdiERqQmQxDRikIy4C8Jq2opskbKLUZeVWJ6gJtSWLFcSeWULa9cB4lLMO
    255kldII.QSHcMKi59FFdI4koHJ.8aqoKnjAgM1CkvpieSoHjCUBoK2i99ce
    B9abaNN2jQfawJ.nnE.cIiQ2pIBGc9fqTwJVyja9gmmvTF8L0gMMEZRIigc5
    oG6JMuhKKPFtc9cxgT3TdQ7XPmb5g1ToER6snynYmWVjYDajrx+gejclbZ3n
    d83SO+16DlJU4gm9ZlC+Nla2zvAtlIX4CIvMXIKGi9nSO8sEJWrm+D5Wct2O
    oIX9igK5VQGC+veA9v5fKExO9XFTJc4e+dhQUqyGJ+9qYPuVMEbiUHYVgR9l
    wD4FyWtqOUcRlfNgyfNwSPmjYPmnInypYPmt21OpPweRHmufUUsmqM8CFzn8
    uhsJcW3h.HTHcgQPnluWLL9XHCS2Zoss94ZsyS1rz8zDdmpfqk0hdOYqxO5+
    L.YVlXO
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`loadbang`, `thispoly~`, `s #0_instance`
: パッチが読み込まれたタイミングで実体IDを取得。
  取得したIDを利用して送受信先を作成するために点在する`sprintf`へ送信する。
  
  同一の実体内に点在する`sprintf`にのみIDを送信しなければならないため、
  送信先の名前は`#0_instance`と`#0`を利用している。

`r #0_instance`, `sprintf #1_%ld_signal`, `prepend set`, `receive~ signal`
: 同一実体内の`send`からIDを取得、`sprintf`オブジェクトを用いて送信先を作成する。

  ここでは引数による名前空間も利用している。引数が *Test* 、
  IDが *1* の場合、 *Test_1_signal* という名前が作成される。

  名前を設定するために`prepend`オブジェクトを用いて接頭辞 *set* を付加、
  `receive~`オブジェクトに新しい名前を設定

実体それぞれに別々のメッセージ/シグナルを送受信することができることを検証する。
サンプルパッチは以下

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-individual-signal/03-mother.png %}{% endcapture %}
{% assign caption = '

メインパッチ

    ----------begin_max5_patcher----------
    1077.3oc4Z0saaBCF85jmBDWmE4+v.6pJsGfcwtbZpxk3lREAh.R+YUqO6Cr
    MsYaIzuvrimVuwR3P7wmyw9ye13mlOK7ppGjMgAeL3qAyl8z7YyTU0WwLyyy
    B2HdHqPzndsvMxlFwZY3B8u0JenUUe0VYY.an5xcap10VHaU+Iro1s0xFYYq
    nMup7xZYVqFXbRzRzh.LuuDYJB9l4Ooam1G2J0ucX3K+zVQa1M4kqOPaoJYw
    8kDxdsV9Jcu8pa+PDZn21z9Xgp0C2q+mWNz8I808i4y6KVXCYhNMYhhsmLYZ
    KHxD1WxDYRxTL2ZpTL3wRrTGJRxuuRj87gECzHdrpGyiSUc+ngxC18S7fGyY
    KmjAmFor0HJ0BVbDRMQfSYp1DMpIy8fJ8A7RNdZSDRhsmNQoIv0oXOnSXzxj
    IISbRpVlRrxxOH3xTjOjokuB6gkoSjvwv4Kyg7sTdeGF+AcqC97W9D9R1k2I
    J5HlMYNKUO8ROKCiniwcJz.rHqycpC3tIZPTJFB2i8F2INf6lo3.4N2abG6.
    tSvm.0ibH0ax5xP7sRI5HoOnSEhFEev38GelNKRkSYJZn7frF6gfbxLY9cxm
    Gh00ju9z88t+Ton.RDOFQErGyXiZ+DfBA97Z+wI7Sz9MA6fY+Tea+TmY+C5.
    L6m8uo8ax1iFwgZ+l38freRpusehyr+Ac.l8i7s8erEuf4iI91GwNyGMx.Ha
    jD6Pa75hpt13vDjbzzSVDDdknb8qT75p5MBE23iszkZQeBO4s2mB4kb11JpE
    ajsx5Kkkhqz7G8+gfPob3BB6cffXBuASPHuCDDcbBX5AxS5wQBk1r2t92IO4
    EJLAKnH8w+pOgviwcrKyycb9QmH+Ly8AxOl23GYh7yLUFH+3die3IxO8LSfz
    KwCzSb+yc8KWrATMkSUG7fNQtiQ73yeFbc62X09aBumNSL1CgnB6Z9LcGiid
    va66otwbMQkFL2QyKE51rnNwboS0bGR5Bj4hQme2ssN2MlqIjrwbGeSGQd0b
    IS0bGRfDj45gEcxdLqP5F2Uufjwb4jQ+HPdMrLdplqIYXPdqKSHbz6KA19G0
    Oj69fGNxysUEO9bPeY.K3h9McTTHKBvAWzdSsTrJqZWYaPRvEh505jslniGa
    xjbzuzAdBFtpcBKxK+8aekpqzW+upJMU6pyFLtgauTvqcmUxl17R0oct2Kg0
    uyAEdv.gOS.0eGZNK.0+wXeSf5urGcCn9KQJFBRw1.INDjhrARQPPhYCjH.P
    hZkQDLHHQsARPl1RRs.RDHi8HI1.IHi8vDafDDeByrARPF6g41.IPAxsgOAY
    .gMvATbbjMVZB.P1vhfLpyFCuoSZAPcpDhsauSV2XdYEDcoRcaUc+iwKTOlW
    pejpdrVdW9v6yT0Hp6xBpsKEnc05zXdHQuQkvMUqj0k6xMowzg7Ol+SfF0RX
    j
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-individual-signal/04-poly.png %}{% endcapture %}
{% assign caption = '

`poly~`サブパッチ

    ----------begin_max5_patcher----------
    802.3oc2XssbaCBD8Y4uBMjou45APWc+U5zwCVhjRFYPCfcbZlju8JgjbSZs
    w3FjUl7hkEBoCmc2ytK7zr.vZwdpBD9svuGFD7zrf.yPsCDzee.XCYeQEQYl
    F31JAe6Fv7tG07WwVcEUadHteztgzOVS69x.v7PvZB+NP3O5mxsB4Fht8wo8
    iTSzE+jwuakjVn6dObZxB37PTJbQx7vbT6MwKW.O7UXkl0jX88eEmAN7gjjM
    TMUthxIqqLqAX+yT5G6F.7JFv3CD.cX0w0J1uLyLJcArczmmMq8m4NZm3zGZ
    VVCnno60clOg7Ahr731O3YME3Tb6kDio.iOgo.Cbjt9iWxvafqXbklvKnGmc
    nSFcbfDGg3QYQlXfXyk7HqDOxQh6QGZsjVS4kgpleFAdig4FdGak2wWeGtpV
    x35aCuAs5KUkq1QpZ32X33yLF.DNwpEHYBr.Md7WBUr6NIwsnmQouRNmszJ2
    PehjyHblyxYT9TJm0i.s6UyYVUynkStZ1VP86yu6lZFNhV.Ugnl9xEKXiglE
    dh4RpgLYvS3.Scb4i8nfkVPY67pTcn6KX7PeWVhYmf7uijTsm1YXGTpQeDp6
    tcL75w3NcJ1J++L0n4fe2g5RnqecowqfbpqrFd8Ys57r1VJajImUlwulashy
    DTxsRPJM6I9xblucazVXsoNEzFoS+zjytOH1gT1IeD5shSp7O6GxXaMNeB1n
    n9mLUsn5wWtvyLpwl0drQsWbHbO1bhHI41H+DTstusLy1Dubud+KcdeeeOZK
    serP+Gz27c.UL9eeHglkR63u0lnDakECK+9bLg+Y0TRUZFmnYB9qlST2bNpU
    2UbRb.GrGvI1AbR7.NQNfyROfS6wlddCmOrbXmbQw9.o3qFmbwMg8Q7P6VhO
    KRHuDQ3BkPuYRBYIUd5Lm9DYT5wQF9tPdvv4DoeeHk6BRPefjS5sLefjKp.j
    OTAHmBQ7Q8DDxEj7QNDjKJ6+MsXWkYRc8NpT0OYCFM8kbuvnIxlatkw6tMxb
    qjtiML+XyHDYSSE5lNJ1J65JXed2NC.aDMpK9VVuztA4mm8aE.hZ3C
    -----------end_max5_patcher-----------

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

実体毎に正弦波、三角波、矩形波、鋸波、および個々の数値を外から内側へ`send`/`receive`。
入力されたシグナル/メッセージはそのまま内側から外へ`forward`/`receive`する。

{% capture url %}{{ site.github.url }}{% link _docs/Max/images/poly-individual-signal/05-result.png %}{% endcapture %}
{% assign caption = '個々の実体へ別々のシグナル/メッセージが送受信できていることが分かる。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 利点と注意点

内外で送受信するためのオーバーヘッドがとても大きいが、
Maxでエクスターナルなどを作成することなく、サブパッチのシグナル処理をゴリ押しで
スマートにマルチスレッド化する唯一の方法になる。
これを用いれば処理にCPU1コアしか使っていない状況にはならない。

一方で初期化の`loadbang`のタイミングが煩雑になる。
外側のUIで初期値を送信する前に、
`poly~`と接続が確立していなければ内部の変数を初期化することができない。

初期化する順番は

1. `poly~`内部の`buffer~`オブジェクトの名前
2. `poly~`内`buffer~`オブジェクトのチャンネル数、長さ等
3. `send`/`receive`
4. 変数の初期化

となる。
