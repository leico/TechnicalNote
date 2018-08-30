---
layout : post
title  : "QMK : 現状のキーマップ"
date   : 2018/08/28
lastchange : 2018-08-31 05:16:57.
tags   :
  - 自作キーボード
  - キーマップ
  - QMK firmware
  - layer
---

## キーマップを再構成する

デフォルトのキーマップが使いにくいのでカスタマイズした。その記録。
早速カスタマイズしたキーボードで入力中。

参考:


## 当初に考えていたこと


記号入力全般
: 記号が既存の配列だと右小指と数字列に集中してしまっている。
これを改善する。

記号の配置
: USキーボードに変えた時にびっくりしたのが記号位置の統一感だった。
セミコロンとコロン、シングル/ダブルクォーテーションの切り替えや
括弧が隣同士に並んでいる配列がとても覚えやすかった。なので
関連ある記号を隣接させたり切り替えで入力させたりするように配置する。


## 作成しながら変化してきたこと

親指シフト、レイヤー切り替え
: 数字列が足りなくて一番困ったのは Modifier キーの位置かもしれない。
Esc キーと Backspace キーの位置と他キーとの兼ね合いにものすごく悩んだ。
結果として親指シフトとなった。

矢印キーは H 、 J 、 K 、 L キーで入力をする
: Vim を使っているのでここにあるとうれしい。
結果としてキーボード右下のスペースがあいて次のキー配列が実現する。

左右対称のシフトとレイヤーキー
: 入力を行うキーと逆の手で Modifier キーを押したいを実現した。

Numpad の実装
: レイヤーの量がそれなりに増えたので余った部分で Numpad を実装することができた。
Numpad にショートカットを割り当てているソフトウェアに有効。

## Part2

### キーマップ

```c
/* Qwerty
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   Q  |   W  |   E  |   R  |   T  ||   Y  |   U  |   I  |   O  |   P  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |   A  |   S  |   D  |   F  |   G  ||   H  |   J  |   K  |   L  |   ;  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |   Z  |   X  |   C  |   V  |   B  ||   N  |   M  |   ,  |   .  |   !  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |      |      |      |      |      |LSFT_T||RSFT_T|      |      |      |      |      |
 * |Reset | Alt  | GUI  |LLower|LRaise|Space ||Space |RRaise|RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Raise
 * ,------------------------------------------------------------------------------------.
 * | Esc  |      |   &  |   {  |   }  |   %  ||   \  |   [  |   ]  |   #  |      | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |   ~  |   |  |   (  |   )  |   *  ||   /  |   <  |   >  |   '  |   :  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |   `  |   ^  |   @  |   $  |   +  ||   -  |   =  |   _  |   "  |   ?  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |      |      |      |      |      |LSFT_T||RSFT_T|      |      |      |      |      |
 * |Reset | Alt  | GUI  |LLower|LRaise|Space ||Space |RRaise|RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Lower
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   1  |   2  |   3  |   4  |   5  ||   6  |   7  |   8  |   9  |   0  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |      |VolDn |VolUp | Mute |      || Left | Down |  Up  |Right |      |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |      |      |      |      |      ||      |      |      |      |      | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |      |      |      |      |      |LSFT_T||RSFT_T|      |      |      |      |      |
 * |Reset | Alt  | GUI  |LLower|LRaise|Space ||Space |RRaise|RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Neutral
 * ,------------------------------------------------------------------------------------.
 * | Esc  |  F1  |  F2  |  F3  |  F4  |      ||   +  |   -  |   1  |   2  |   3  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |  F5  |  F6  |  F7  |  F8  |      ||   *  |   /  |   4  |   5  |   6  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |  F9  |  F10 |  F11 |  F12 |      ||   .  |   ,  |   7  |   8  |   9  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |      |      |      |      |      |LSFT_T||RSFT_T|      |      |   0  |      |      |
 * |Reset | Alt  | GUI  |LLower|LRaise|Space ||Space |RRaise|RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
```

Part1 からの主な変更点は Raise レイヤーの数字列を廃止して記号のみにしてしまったこと。
この関係で 1 つレイヤーが少なくなっている。数字列は Lower レイヤーに任せることにした。




### QWERTY レイヤー

Part1 からほとんど変更がないが、Alt キーと GUI キーを入れ替えた。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/07_part2_qwerty.png %}{% endcapture %}
{% capture caption %}

黄色部分を入れ替えた

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




### Raise レイヤー

一番の変更点。関連するものを隣接させるのは踏襲しつつ、数字列を Part1 の Raise Combo レイヤーに置き換えて、
若干の配置変更をおこなった。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/08_part2_raise.png %}{% endcapture %}
{% capture caption %}

Raise レイヤー

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}





### Lower レイヤー

特に変更なし。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/09_part2_lower.png %}{% endcapture %}
{% capture caption %}

Lower レイヤー

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



### Neutral レイヤー

Part1 の Raise  Combo レイヤー。 Raise と Lower レイヤーが同時に有効になった時、このレイヤーに変化する。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/10_part2_neutral.png %}{% endcapture %}
{% capture caption %}

Neutral レイヤー

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## Part1

### キーマップ

こんな感じになった。

```c
/* Qwerty
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   Q  |   W  |   E  |   R  |   T  ||   Y  |   U  |   I  |   O  |   P  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |   A  |   S  |   D  |   F  |   G  ||   H  |   J  |   K  |   L  |   ;  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |   Z  |   X  |   C  |   V  |   B  ||   N  |   M  |   ,  |   .  |   !  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |      |      |      |      |      |LSFT_T||RSFT_T|      |      |      |      |      |
 * |Reset | GUI  | Alt  |LLower|LRaise|Space ||Space |RRaise|RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Raise
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   1  |   2  |   3  |   4  |   5  ||   6  |   7  |   8  |   9  |   0  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |   ~  |   |  |   (  |   )  |   +  ||   -  |   <  |   >  |   '  |   :  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |   `  |   ^  |   @  |   $  |   *  ||   /  |   =  |   _  |   "  |   ?  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |Reset | GUI  | Alt  |Shift |LRaise|Combo ||Combo |RRaise|Shift |      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Raise_Combo
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   1  |   2  |   3  |   4  |   5  ||   6  |   7  |   8  |   9  |   0  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |      |   &  |   {  |   }  |   #  ||   %  |   [  |   ]  |      |      |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |      |      |      |      |      ||   \  |      |      |      |      | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |Reset | GUI  | Alt  |Shift |LRaise|Combo ||Combo |RRaise|Shift |      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Lower
 * ,------------------------------------------------------------------------------------.
 * | Esc  |   1  |   2  |   3  |   4  |   5  ||   6  |   7  |   8  |   9  |   0  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |      |VolDn |VolUp | Mute |      || Left | Down |  Up  |Right |      |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |      |      |      |      |      ||      |      |      |      |      | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |Reset | GUI  |  Alt |LLower|Shift |Combo ||Combo |Shift |RLower|      | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
/* Lower_Combo
 * ,------------------------------------------------------------------------------------.
 * | Esc  |  F1  |  F2  |  F3  |  F4  |      ||   +  |   -  |   1  |   2  |   3  | Bksp |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Ctrl |  F5  |  F6  |  F7  |  F8  |      ||   *  |   /  |   4  |   5  |   6  |Enter |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * | Tab  |  F9  |  F10 |  F11 |  F12 |      ||   .  |   ,  |   7  |   8  |   9  | GUI  |
 * |------+------+------+------+------+------++------+------+------+------+------+------|
 * |Reset | GUI  | Alt  |LLower|Shift |Combo ||Combo |Shift |RLower|   0  | Del  |Reset |
 * `------------------------------------------------------------------------------------'
 */
```

### QWERTY レイヤー

ほぼそのままで一般的なローマ字入力とちょっとした記号の入力にしか使わない。
大文字はスペースキーをホールドした際に Shift になる機能で実装。普通に入力をする場合は
スペースとして動作する。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/01_shift_space.png %}{% endcapture %}
{% capture caption %}

青色の部分を普通に入力すると Space が入力され、長押しすると Shift キーになる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### レイヤー切り替え

レイヤーは5種類用意した。通常の QWERTY 配列、 Lower 配列、 Raise 配列、あとそれぞれに Combo レイヤー
というものが存在する。

レイヤー切り替えは主に下部の Modifier キーにて行う。
どのような組み合わせでレイヤーが切り替わるかを以下に示す。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/02_switch_layer.png %}{% endcapture %}
{% capture caption %}

最下部キーの組み合わせ

QWERTY
: 何も押してない状態。デフォルトのレイヤー

Lower
: 左右どちらかの Lower キーを押している間 Lower レイヤーになる。
Lower キーが押されている時、 Raise キーは Shift キーに変化し、
Space キーは Combo キーに変化する。

Raise
: 左右どちらかの Raise キーを押している間 Raise レイヤーになる。
Raise キーが押されている時、 Lower キーは Shift キーに変化し、
Space キーは Combo キーに変化する。

Lower Combo
: Lower レイヤーの状態で Combo キーを押している間 Lower Combo レイヤーになる。
Lower キーを離せば QWERTY レイヤーに、 Combo キーを離せば Lower レイヤーに戻る。

Raise Combo
: Raise レイヤーの状態で Combo キーを押している間 Raise Combo レイヤーになる。
Raise キーを離せば QWERTY レイヤーに、 Combo キーを離せば Raise レイヤーに戻る。

どのキーも左右両方に配置されている。
両方とも押したところで特に変化はない。

Combo レイヤーがあるので Lower、 Raise レイヤーに Shift キーは不要だが、
ショートカットの入力で必要なことがあるので残している。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### Raise レイヤー

数字と入れれるだけの記号をなるべく関連あるように詰め込んだ。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/03_raise.png %}{% endcapture %}
{% capture caption %}

括弧類
: Raise Combo レイヤー含めて人差し指、中指の辺りで全ての括弧が完結する。(画像青紫)

四則演算
: 中央にまとめて配置(画像ピンク)

コロン類、感嘆符類
: QWERTY 側と統一性を持たせて同じ位置に配置した。(画像ミドリ)

似た記号など
: 通常配列の場合 Shift で切り替わる文字を上下に配置(画像水色)

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### Raise Combo レイヤー

Raise レイヤーに収まらなかった記号と残りの記号を入れてある。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/04_raise_combo.png %}{% endcapture %}
{% capture caption %}

括弧類(青紫) とバックスラッシュ(黄色) は Raise 側と統一性を持たせている。


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



### Lower レイヤー

Vim 配列でのカーソル移動を実装したのと、左側に何もないのもあれなので
音量変更ができるようにした。

{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/05_lower.png %}{% endcapture %}
{% capture caption %}

数字キーはこちら側でも打てる(水色)。
左側にボリューム変更(ピンク色)、右側にカーソル移動(ミドリ色)がある。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



### Lower Combo レイヤー

ここに Function ボタンと Numpad を押し込んだ。


{% capture url %}{{ site.github.url }}{% link _docs/QMK/images/key-layout/06_lower_combo.png %}{% endcapture %}
{% capture caption %}

Function キー(黄色) と Numpad(水色)

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



