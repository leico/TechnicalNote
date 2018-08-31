---
layout : post
title  : "QMK : キーマップのカスタマイズ"
date   : 2018/08/28
lastchange : 2018-08-31 21:26:56.
tags   :
  - 自作キーボード
  - キーマップ
  - QMK firmware
  - layer
---

## いきなりカスタマイズするのはハードルが高い

いろいろなサイトを参考にしながらカスタマイズをした。
実現したい機能ベースで参考をにしたページをまとめる。

参考:

* [ASCII.jp：カスタムキーマップで自作キーボードを自分の分身とする (2/4)｜KTUの自作キーボー道](http://ascii.jp/elem/000/001/645/1645504/index-2.html)
* [what is now the correct way to build community keymaps · Issue #2492 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/2492)
* [qmk_firmware/feature_layouts.md at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_layouts.md)
* [qmk_firmware/custom_quantum_functions.md at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/docs/custom_quantum_functions.md)
* [QMK Firmware Cheatsheet 0.3](https://jayliu50.github.io/qmk-cheatsheet/)
* [Keycodes - QMK Firmware](https://docs.qmk.fm/#/keycodes)
* [US ANSI Shifted Keys - QMK Firmware](https://docs.qmk.fm/#/keycodes_us_ansi_shifted)
* [キーコードまとめ :: ErgoDox \| Refills](https://syon.github.io/refills/rid/1471100/)
* [Advanced Keycodes - QMK Firmware](https://docs.qmk.fm/#/feature_advanced_keycodes)
* [qmk tap/hold delay : olkb](https://www.reddit.com/r/olkb/comments/4zmior/qmk_taphold_delay/)
* [Keymap Overview - QMK Firmware](https://docs.qmk.fm/#/keymap)
* [qmk_firmware/action.h at ee700b2e831067bdb7584425569b61bc6329247b · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/ee700b2e831067bdb7584425569b61bc6329247b/tmk_core/common/action.h)
* [qmk_firmware/keymap.c at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/keyboards/vitamins_included/keymaps/default/keymap.c)
* [Advanced Keycodes - QMK Firmware](https://docs.qmk.fm/#/feature_advanced_keycodes)
* [qmk_firmware/action_layer.h at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/tmk_core/common/action_layer.h)
* [qmk_firmware/action_layer.c at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/tmk_core/common/action_layer.c)
* [Useful Functions - QMK Firmware](https://docs.qmk.fm/#/ref_functions)
* [QMKでのレイヤー機能の挙動について - hrhg memo](http://hrhg.hatenablog.com/entry/2017/07/22/041303)
* [qmk_firmware/readme.md at f39e1b5dfe7552f01dbc6eab95c268f41a9d98e2 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/f39e1b5dfe7552f01dbc6eab95c268f41a9d98e2/readme.md#prevent-stuck-modifiers)



## キーマップのソースコードの場所

{% capture text %}

ゼロからキーマップを作るのは現実的でないので、まずは既存のものをコピーしてそれを編集するとしよう。
各キーボードの「Keymaps」フォルダーがあり、
その中にカスタムファームウェアをビルドするためのファイルが格納されている。
まずはここから叩き台となるキーマップをコピーするのだ。

Keymapsフォルダーの中には標準キーマップを示す「defalut」フォルダーのほかに、
ユーザーが作ったキーマップのフォルダーが収録される。
今回は無難なdefaultをコピーしたが、他のユーザーが作ったキーマップをお手本にするのもいいだろう。 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASCII.jp：カスタムキーマップで自作キーボードを自分の分身とする (2/4)｜KTUの自作キーボー道](http://ascii.jp/elem/000/001/645/1645504/index-2.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


実際この通りで、 _keymap_ ディレクトリ内に _keymap/default_ を別名でコピーしたら
新しいキーマップとして認識される。

作ったキーマップを `avrdude` で `make` するには

```sh
make vitamins_included/rev1:part1:avrdude
```

のように

```sh
make [keyboard]:[keymap name]:[firmware]
```

という書式で指定してあげればよい。上記の例の場合、 
_default_ を _part1_ という名前でコピーしたものを `make` している。




## _layouts_ ディレクトリのキーマップを利用する

_layouts_ ディレクトリのレイアウトを利用することもできる。
自分のキーボードの配列と共通したレイアウトのディレクトリから選択しなければならないが、
後は上記と同じ方法でコンパイル & 書き込みができる。

{% capture text %}

Have tried 
`make ergodox:swedish-lindhe` as well as 
`make keyboard=ergodox keymap=community/swedish-lindhe` but neither works.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [lindhe](https://github.com/lindhe)
from [what is now the correct way to build community keymaps · Issue #2492 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/issues/2492)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





## _layouts_ ディレクトリにキーマップを作成をする

_keyboards/*/keymaps_ ではなく、 _layouts_ に作ったものを利用することもできる。
実際に _keymaps_ ディレクトリに作成したキーマップを _layouts_ ディレクトリに移動し、利用してみた。


{% capture text %}
## Supporting a Layout

For a keyboard to support a layout, the variable must be defined in it's `<keyboard>.h` , 
and match the number of arguments/keys (and preferably the physical layout):

```c
#define LAYOUT_60_ansi KEYMAP_ANSI
```

The name of the layout must match this regex: `[a-z0-9_]+`

The folder name must be added to the keyboard's `rules.mk`:

```make
LAYOUTS = 60_ansi
```

`LAYOUTS` can be set in any keyboard folder level's `rules.mk`:

```make
LAYOUTS = 60_iso
```

but the `LAYOUT_<layout>` variable must be defined in `<folder>.h` as well.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/feature_layouts.md at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_layouts.md)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


まず該当する _layouts_ ディレクトリへ移動させる。移動させるディレクトリは _keyboards/*/rules.mk_ 内の 
`LAYOUTS` に代入されている値が手がかりとなる。

vitamins_included の場合

```make
LAYOUTS = ortho_4x12
```

なので _layouts/community/ortho_4x12_ 内にディレクトリを移動させればよい。

```sh
$ mv part1 ../../../layouts/community/ortho_4x12/leico_part1
$ mv part2 ../../../layouts/community/ortho_4x12/leico_part2
```

これだけで `make` が通ればよかったのだが

```sh
$ make vitamins_included/rev1:leico_part2:avrdude
QMK Firmware 0.6.98

Making vitamins_included/rev1 with keymap leico_part2 and target avrdude

avr-gcc (GCC) 8.1.0
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Compiling: keyboards/vitamins_included/matrix.c                                                    In file included from <command-line>:
./layouts/community/ortho_4x12/leico_part2/config.h:24:10: fatal error: ../../config.h: No such file or directory
 #include "../../config.h"
          ^~~~~~~~~~~~~~~~
compilation terminated.
 [ERRORS]
 | 
 | 
 | 
make[1]: *** [.build/obj_vitamins_included_rev1_leico_part2/matrix.o] Error 1
make: *** [vitamins_included/rev1:leico_part2:avrdude] Error 1
Make finished with errors
```

`../../config.h` のインクルードでエラーが発生した。 元のディレクトリ位置から `../../config.h` を探索し、
必要そうな設定をコピーして再度 `make` することで書き込みに成功した。

`config.h` の中身はこのようになった。

```c
#ifndef CONFIG_USER_H
#define CONFIG_USER_H

#include "config_common.h"

#if !defined(NO_DEBUG) && !defined(CONSOLE_ENABLE)
#define NO_DEBUG
#endif // !NO_DEBUG
#if !defined(NO_PRINT) && !defined(CONSOLE_ENABLE)
#define NO_PRINT
#endif // !NO_PRINT
#define NO_ACTION_MACRO
#define NO_ACTION_FUNCTION

#define DISABLE_LEADER

// hold & tapping delay setting
#define TAPPING_TERM 100

/* Use I2C or Serial, not both */

#define USE_SERIAL
// #define USE_I2C

/* Select hand configuration */

//#define MASTER_LEFT
// #define MASTER_RIGHT
#define EE_HANDS

#ifdef AUDIO_ENABLE
  #define DEFAULT_LAYER_SONGS { SONG(QWERTY_SOUND), \
                                SONG(DVORAK_SOUND), \
                                SONG(COLEMAK_SOUND) \
                              }
#endif

#endif
```





## 独自のキーコードを定義する

レイヤーのスイッチを行うキー場合、よく見られるのが独自のキーコードを定義している方法。

{% capture text %}

### Defining a New Keycode

The first step to creating your own custom keycode(s) is to enumerate them. 
This means both naming them and assigning a unique number to that keycode. 
Rather than limit custom keycodes to a fixed range of numbers QMK provides the `SAFE_RANGE` macro. 
You can use `SAFE_RANGE` when enumerating your custom keycodes to guarantee that you get a unique number.

Here is an example of enumerating 2 keycodes. 
After adding this block to your `keymap.c` you will be able to use FOO and BAR inside your keymap.

```c
enum my_keycodes {
  FOO = SAFE_RANGE,
  BAR
};
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/custom_quantum_functions.md at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/docs/custom_quantum_functions.md)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

独自のキーコードを定義するには上記のように、 `enum` で好きな名前で定義をする。
他のキーコードと被らないように、最初の値が `SAFE_RANGE` で定義されているので、
この値から開始させるのがよいみたい。






## レイヤーを用意する

切り替えるべきレイヤーを用意する。


{% capture text %}


## Step 1: Declare

Create each layer as an entry in an enum. Replace YOUR_LAYER_1, YOUR_LAYER_2, etc., 
below, with names of your layers.

_in keymap.c:_

```c
// Layer Declarations
enum {
    YOUR_LAYER_1 = 0,
    YOUR_LAYER_2,
    // ..., the rest of your layers
};
```


## Step 2: Define

Add the keycodes for each layer into the keymaps array, 
by calling KEYMAP() for each layer.

_in keymap.c, create your KEYMAPs:_

```c
// Layer Definitions
const uint16_t PROGMEM keymaps[][MATRIX_ROWS][MATRIX_COLS] = {

  [YOUR_LAYER_1] = KEYMAP(
    // ... list all your keycodes here, separating each with comma
  ),

  [YOUR_LAYER_2] = KEYMAP(
    // ... list all your keycodes here, separating each with comma
  ),

  // ..., the rest of your layers

};
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[QMK Firmware Cheatsheet 0.3](https://jayliu50.github.io/qmk-cheatsheet/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


このように `enum` でレイヤーの重なり順を定義、そしてそれぞれのレイヤーのキーマップを `keymaps` 配列に定義する。
ただ、キーマップを定義するマクロが変更になっている可能性がある。 Let's Split - Vitamins included では

```c
LAYOUT_ortho_4x12( 
// .....
)
```

というマクロに変更されていた。



## レイヤーにキーマップを配置する

_default_ を参考にすればよい。各種キーの値が既に定義されているのでそれを順番に配置してゆく。

```c
/* Qwerty
 * ,-----------------------------------------------------------------------------------.
 * | Esc  |   Q  |   W  |   E  |   R  |   T  |   Y  |   U  |   I  |   O  |   P  | Bksp |
 * |------+------+------+------+------+-------------+------+------+------+------+------|
 * | Tab  |   A  |   S  |   D  |   F  |   G  |   H  |   J  |   K  |   L  |   ;  |  '   |
 * |------+------+------+------+------+------|------+------+------+------+------+------|
 * | Shift|   Z  |   X  |   C  |   V  |   B  |   N  |   M  |   ,  |   .  |   /  |Enter |
 * |------+------+------+------+------+------+------+------+------+------+------+------|
 * | Ctrl | GUI  |  Alt |Adjust|Lower |Space |Space |Raise | Left | Down |  Up  |Right |
 * `-----------------------------------------------------------------------------------'
 */
[_QWERTY] = LAYOUT_ortho_4x12( \
  KC_ESC,  KC_Q,    KC_W,    KC_E,    KC_R,    KC_T,    KC_Y,    KC_U,    KC_I,    KC_O,    KC_P,    KC_BSPC, \
  KC_TAB,  KC_A,    KC_S,    KC_D,    KC_F,    KC_G,    KC_H,    KC_J,    KC_K,    KC_L,    KC_SCLN, KC_QUOT, \
  KC_LSFT, KC_Z,    KC_X,    KC_C,    KC_V,    KC_B,    KC_N,    KC_M,    KC_COMM, KC_DOT,  KC_SLSH, KC_ENT , \
  KC_LCTRL,KC_LGUI,  KC_LALT, ADJUST,  LOWER,   KC_SPC,  KC_SPC,  RAISE,   KC_LEFT, KC_DOWN, KC_UP,   KC_RGHT \
)
```

コメントにこのようにキー配列が書かれているのは親切でわかりやすい。

各種キーコードは以下のページにまとめられている。

[Keycodes - QMK Firmware](https://docs.qmk.fm/#/keycodes)

ページ後方のほうで少しだけ特殊なキーコードに触れる。





## 通常 Shift 押しで入力するキーを割り当てる

`#` といった、通常 Shift を押しながら入力するキーを割り当てるキーコードも定義されている。

{% capture text %}

## Keycodes

|Key                     |Aliases            |Description        |
|------------------------|-------------------|-------------------|
|`KC_TILDE`              |`KC_TILD`          |`~`                |
|`KC_EXCLAIM`            |`KC_EXLM`          |`!`                |
|`KC_AT`                 |                   |`@`                |
|`KC_HASH`               |                   |`#`                |
|`KC_DOLLAR`             |`KC_DLR`           |`$`                |
|`KC_PERCENT`            |`KC_PERC`          |`%`                |
|`KC_CIRCUMFLEX`         |`KC_CIRC`          |`^`                |
|`KC_AMPERSAND`          |`KC_AMPR`          |`&`                |
|`KC_ASTERISK`           |`KC_ASTR`          |`*`                |
|`KC_LEFT_PAREN`         |`KC_LPRN`          |`(`                |
|`KC_RIGHT_PAREN`        |`KC_RPRN`          |`)`                |
|`KC_UNDERSCORE`         |`KC_UNDS`          |`_`                |
|`KC_PLUS`               |                   |`+`                |
|`KC_LEFT_CURLY_BRACE`   |`KC_LCBR`          |`{`                |
|`KC_RIGHT_CURLY_BRACE`  |`KC_RCBR`          |`}`                |
|`KC_PIPE`               |                   |`|`                |
|`KC_COLON`              |`KC_COLN`          |`:`                |
|`KC_DOUBLE_QUOTE`       |`KC_DQUO`, `KC_DQT`|`"`                |
|`KC_LEFT_ANGLE_BRACKET` |`KC_LABK`, `KC_LT` |`<`                |
|`KC_RIGHT_ANGLE_BRACKET`|`KC_RABK`, `KC_GT` |`>`                |
|`KC_QUESTION`           |`KC_QUES`          |`?`                |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[US ANSI Shifted Keys - QMK Firmware](https://docs.qmk.fm/#/keycodes_us_ansi_shifted)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






## 長押しで Shift 、タップで Space のような挙動を行う


{% capture text %}

### Modifier-Tap shortcuts

長押しで Modifier キー、タップで通常キーを実現する `MT(mod, kc)` の簡易記述です。

| Keycode      | Desc                                              |
|--------------|---------------------------------------------------|
| `CTL_T(kc)`  | 長押しで Ctrl 、タップで _kc_                     |
| `SFT_T(kc)`  | 長押しで Shift 、タップで _kc_                    |
| `ALT_T(kc)`  | 長押しで Alt 、タップで _kc_                      |
| `GUI_T(kc)`  | 長押しで Gui 、タップで _kc_                      |
| `ALL_T(kc)`  | 長押しで Ctrl + Shift + Alt + Gui 、タップで _kc_ |
| `MEH_T(kc)`  | 長押しで Ctrl + Shift + Alt 、タップで _kc_       |
| `LCAG_T(kc)` | 長押しで Ctrl + Alt + Gui 、タップで _kc_         |

#### Samples for Modifier-Tap

`CTL_T(KC_Z)`
: 長押しで `Ctrl` 、タップで `Z`。

`ALL_T(KC_NO)`
: 長押しで `Hyper` 、タップで `何もしない`。

`ALT_T(KC_APP)`
: 長押しで `Alt` 、タップで `APP`。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[キーコードまとめ :: ErgoDox \| Refills](https://syon.github.io/refills/rid/1471100/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}


For convenience, QMK includes some Mod-Tap shortcuts to make common combinations more compact in your keymap:

|Key         |Aliases                                |Description                                            |
|------------|---------------------------------------|-------------------------------------------------------|
|`LCTL_T(kc)`|`CTL_T(kc)`                            |Left Control when held, `kc` when tapped               |
|`RCTL_T(kc)`|                                       |Right Control when held, `kc` when tapped              |
|`LSFT_T(kc)`|`SFT_T(kc)`                            |Left Shift when held, `kc` when tapped                 |
|`RSFT_T(kc)`|                                       |Right Shift when held, `kc` when tapped                |
|`LALT_T(kc)`|`ALT_T(kc)`                            |Left Alt when held, `kc` when tapped                   |
|`RALT_T(kc)`|`ALGR_T(kc)`                           |Right Alt when held, `kc` when tapped                  |
|`LGUI_T(kc)`|`LCMD_T(kc)`, `RWIN_T(kc)`, `GUI_T(kc)`|Left GUI when held, `kc` when tapped                   |
|`RGUI_T(kc)`|`RCMD_T(kc)`, `RWIN_T(kc)`             |Right GUI when held, `kc` when tapped                  |
|`C_S_T(kc)` |                                       |Left Control and Shift when held, `kc` when tapped     |
|`MEH_T(kc)` |                                       |Left Control, Shift and Alt when held, `kc` when tapped|
|`LCAG_T(kc)`|                                       |Left Control, Alt and GUI when held, `kc` when tapped  |
|`RCAG_T(kc)`|                                       |Right Control, Alt and GUI when held, `kc` when tapped |
|`ALL_T(kc)` |                                       |Left Control, Shift, Alt and GUI when held, `kc` when tapped - more info [here](http://brettterpstra.com/2012/12/08/a-useful-caps-lock-key/)|
|`SGUI_T(kc)`|`SCMD_T(kc)`, `SWIN_T(kc)`             |Left Shift and GUI when held, `kc` when tapped         |
|`LCA_T(kc)` |                                       |Left Control and Alt when held, `kc` when tapped       |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Advanced Keycodes - QMK Firmware](https://docs.qmk.fm/#/feature_advanced_keycodes)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


長押しで Shift 、タップで Space を実現するには `LSFT_T(KC_SPC)` を割り当てたいキーの位置に記述する。


```c
[ ANY_LAYER ] = LAYOUT_ortho_4x12( ....., LSFT_T(KC_SPC), .....
)
```



## 長押し、タップボタンの挙動が若干重い気がする

上記設定を行った後、スペースの入力が少しもっさりしている気がした。

{% capture text %}

Stick this in your config.h and modify it.

```c
#define TAPPING_TERM 200
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [Resome](https://www.reddit.com/user/Risome)
from [qmk tap/hold delay : olkb](https://www.reddit.com/r/olkb/comments/4zmior/qmk_taphold_delay/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`TAPPING_TERM` で入力までのディレイ、ひいては長押しとタップ判定時間を変更できるようだ。

_config.h_ に `TAPPING_TERM 100` を設定することで解決した。






## レイヤーを切り替える簡単な方法

一番簡単にレイヤーを切り替える方法はキーコードで既に実装されている。

{% capture text %}
## Layer

<https://github.com/jackhumbert/qmk_firmware#switching-and-toggling-layers>

| Keycode           | Mean           | Desc                                            |
|-------------------|----------------|-------------------------------------------------|
| `TO(layer, when)` | GOTO layer     | レイヤー移動（whenは1(ON_PRESS)推奨）           |
| `MO(layer)`       | Momentary      | 押している間だけ指定したレイヤー                |
| `OSL(layer)`      | One-shot layer | 次の１キーだけ指定したレイヤー                  |
| `LT(layer, kc)`   | Layer / Tap    | 押している間だけ指定したレイヤー、タップでkc    |
| `TG(layer)`       | Toggle layer   | タップして指定したレイヤー、再タップで戻る（※） |
| `DF(layer)`       | Default layer  | デフォルトレイヤーの変更（電源OFFまで継続）     |

※ 行き先のレイヤーには同じキーに `KC_TRNS` を割り当てる必要あり

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[キーコードまとめ :: ErgoDox \| Refills](https://syon.github.io/refills/rid/1471100/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

## Step 3: Use

Here are a variety of ways to change the layer.

| _Keycode to be added to your call to KEYMAP()_ | _Description_                                                              |
|------------------------------------------------|----------------------------------------------------------------------------|
| MO(YOUR_LAYER)                                 | While held, MOmentarily switch to YOUR_LAYER.                              |
| LT(YOUR_LAYER, KC_XXXX)                        | Layer Tap. When held: go to YOUR_LAYER. When tapped: send KC_XXXX          |
| TG(YOUR_LAYER)                                 | Layer Toggle. When tapped, toggles YOUR_LAYER on or off                    |
| TO(YOUR_LAYER)                                 | When tapped, goes to YOUR_LAYER                                            |
| TT(YOUR_LAYER)                                 | When tapped, toggles YOUR_LAYER on or off.When held, activates YOUR_LAYER. |
| OSL(YOUR_LAYER)                                | One-Shot Layer. Goes to YOUR_LAYER for the next keypress                   |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[QMK Firmware Cheatsheet 0.3](https://jayliu50.github.io/qmk-cheatsheet/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

以下のようにレイヤーを定義した場合、

```c
enum layers {
    LAYER1 = 0
  , LAYER2
};
```

レイヤー1 から レイヤー2 へ、押しっぱなしの時だけ変更するボタンを作るには

```c
[LAYER1] = LAYOUT_ortho_4x12( ....., MO( LAYER2 ), .....
)
```

という記述をすると実現できる。
ただ切り替えたいだけの場合はこれが有効。





## レイヤーの重なり順と透過設定

{% capture text %}

Respective layers can be validated simultaneously. 
Layers are indexed with 0 to 31 and higher layer has precedence.

```
Keymap: 32 Layers                   Layer: action code matrix
-----------------                   ---------------------
stack of layers                     array_of_action_code[row][column]
       ____________ precedence               _______________________
      /           / | high                  / ESC / F1  / F2  / F3   ....
  31 /___________// |                      /-----/-----/-----/-----
  30 /___________// |                     / TAB /  Q  /  W  /  E   ....
  29 /___________/  |                    /-----/-----/-----/-----
   :   _:_:_:_:_:__ |               :   /LCtrl/  A  /  S  /  D   ....
   :  / : : : : : / |               :  /  :     :     :     :
   2 /___________// |               2 `--------------------------
   1 /___________// |               1 `--------------------------
   0 /___________/  V low           0 `--------------------------
```

Sometimes, the action code stored in keymap may be referred as keycode in some documents due to the TMK history.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Keymap Overview - QMK Firmware](https://docs.qmk.fm/#/keymap)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

数字の小さい方から順番に上にレイヤーがオーバーラップしてゆく仕組みになっている。


{% capture text %}

## Layer Precedence and Transparency

Note that higher layer has higher priority on stack of layers, 
namely firmware falls down from top layer to bottom to look up keycode. 
Once it spots keycode other than `KC_TRNS` (transparent) on a layer 
it stops searching and lower layers aren't referred.

You can place `KC_TRANS` on overlay layer changes just part of layout 
to fall back on lower or base layer. 
Key with `KC_TRANS` (`KC_TRNS` and `_______` are the alias) 
doesn't has its own keycode and refers to lower valid layers for keycode, instead.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Keymap Overview - QMK Firmware](https://docs.qmk.fm/#/keymap)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`KC_TRANS` が設定されたキーはキーコードを持たず、下位レイヤーのキーコードがそのまま利用される。
キーコードが見つかるまで下位レイヤーが探索される。



## 独自キーコードの挙動を定義する

{% capture text %}

## Programming the Behavior of Any Keycode

When you want to override the behavior of an existing key, 
or define the behavior for a new key, you should use the `process_record_kb()` 
and `process_record_user()` functions. These are called by QMK 
during key processing before the actual key event is handled. 
If these functions return `true` QMK will process the keycodes as usual. 
That can be handy for extending the functionality of a key rather than replacing it. 
If these functions return `false` QMK will skip the normal key handling, 
and it will be up to you to send any key up or down events that are required.

These function are called every time a key is pressed or released.
Example  `process_record_user()` Implementation

This example does two things. 
It defines the behavior for a custom keycode called `FOO`, 
and it supplements our Enter key by playing a tone whenever it is pressed.

```c
bool process_record_user(uint16_t keycode, keyrecord_t *record) {
  switch (keycode) {
    case FOO:
      if (record->event.pressed) {
        // Do something when pressed
      } else {
        // Do something else when release
      }
      return false; // Skip all further processing of this key
    case KC_ENTER:
      // Play a tone when enter is pressed
      if (record->event.pressed) {
        PLAY_NOTE_ARRAY(tone_qwerty);
      }
      return true; // Let QMK send the enter press/release events
    default:
      return true; // Process all other keycodes normally
  }
}
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Keymap Overview - QMK Firmware](https://docs.qmk.fm/#/keymap)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

独自で定義したキーコードは、

```c
bool process_record_user(uint16_t keycode, keyrecord_t *record)
```

関数でイベントを取得できる。この関数はキーが押された、または離された時に呼び出される。
キーの状態は `record -> event.pressed` で取得できる。このパラメータは


{% capture text %}

```c
/* Key event container for recording */
typedef struct {
    keyevent_t  event;
#ifndef NO_ACTION_TAPPING
    tap_t tap;
#endif
} keyrecord_t;
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/action.h at ee700b2e831067bdb7584425569b61bc6329247b · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/ee700b2e831067bdb7584425569b61bc6329247b/tmk_core/common/action.h)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

```c
/* key event */
typedef struct {
    keypos_t key;
    bool     pressed;
    uint16_t time;
} keyevent_t;
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/keyboard.h at ee700b2e831067bdb7584425569b61bc6329247b · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/ee700b2e831067bdb7584425569b61bc6329247b/tmk_core/common/keyboard.h)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

と定義されていたので、キーが押されると `true` 、離れると `false` となるようだ。 

この関数自体が `true` を返すと続けて既存のキーコード処理が行われる(存在する場合)。
`false` を返すと既存の処理は行われずに完全な処理の上書きとなる。

自ら定義したキーの挙動は、 keycode で条件分岐を行ってそれぞれ実装してゆく。


## レイヤー関係の関数

{% capture text %}

```c
case LOWER:
      if (record->event.pressed) {
        layer_on(_LOWER);
        update_tri_layer(_LOWER, _RAISE, _ADJUST);
      } else {
        layer_off(_LOWER);
        update_tri_layer(_LOWER, _RAISE, _ADJUST);
      }
      return false;
break;
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/keymap.c at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/keyboards/vitamins_included/keymaps/default/keymap.c)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


vitamins included のキーマップのソースはこのようにしてレイヤーの切り替えを行っていた。
`layer_on` 、 `layer_off` 以外にもレイヤー関係の関数がありそうだ。


{% capture text %}

Sometimes, you might want to switch between layers in a macro or as part of a tap dance routine. 
`layer_on` activates a layer, and `layer_off` deactivates it. 
More layer-related functions can be found in [action_layer.h](https://github.com/qmk/qmk_firmware/blob/master/tmk_core/common/action_layer.h).

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Advanced Keycodes - QMK Firmware](https://docs.qmk.fm/#/feature_advanced_keycodes)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

レイヤーに関する関数は `action_layer.h` に書かれているらしい。
使いそうなものをちょっとだけまとめてみる。

{% capture text %}

```c
extern uint32_t layer_state;
```

---

```c
void layer_clear(void)
{
    layer_state_set(0);
}

/** \brief Layer state is
 *
 * FIXME: Needs docs
 */
bool layer_state_is(uint8_t layer)
{
    return layer_state_cmp(layer_state, layer);
}

/** \brief Layer state compare
 *
 * FIXME: Needs docs
 */
bool layer_state_cmp(uint32_t cmp_layer_state, uint8_t layer) {
    if (!cmp_layer_state) { return layer == 0; }
    return (cmp_layer_state & (1UL<<layer)) != 0;
}


...


/** \brief Layer on
 *
 * FIXME: Needs docs
 */
void layer_on(uint8_t layer)
{
    layer_state_set(layer_state | (1UL<<layer));
}

/** \brief Layer off
 *
 * FIXME: Needs docs
 */
void layer_off(uint8_t layer)
{
    layer_state_set(layer_state & ~(1UL<<layer));
}
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/action_layer.h at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/tmk_core/common/action_layer.h)

[qmk_firmware/action_layer.c at master · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/master/tmk_core/common/action_layer.c)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`layer_state`
: レイヤーの状態は `layer_state` に保存される。何かレイヤーが有効になると、`layer_state` の該当するレイヤーのビットが 1 になる。
`layer_state` は `uint32_t` 型なので最大 32 のレイヤーを保持することができる。という仕組みである。

`layer_on` 
: 該当するレイヤーの番号まで 1 をビットシフトさせ、その結果と `layer_state` の現在値との OR 演算を行う。
結果として対象のレイヤーを有効化する。

`layer_off` 
: 該当するレイヤーの番号まで 1 をビットシフトさせ、その結果を反転したものと `layer_state` の現在値との AND 演算を行う。
結果として対象のレイヤーを無効化する。

`layer_clear`
: レイヤーを全て無効にする。何か1つだけレイヤーを有効にしたい場合の前準備などに使える。

`layer_state_cmp`
: 対象とするレイヤーが、比較対象とする layer_state で有効の場合 `true` 、無効の場合 `false` を返す。
`layer_state_is` の準備関数っぽい。

`layer_state_is`
: 対象のレイヤーが有効か無効かを `true` 、 `false` で返す。


この辺りのものを組み合わせると複雑な切り替えが実現できそうだ。




## 2つのレイヤーが有効になった時に3つ目のレイヤーを有効にする

{% capture text %}

The first is the `update_tri_layer(x, y, z)` function.   
This function check to see if layers `x` and `y` are both on. 
If they are both on, then it runs on layer `z`.  
Otherwise, if both `x` and `y` are not both on (either only one is, or neither is), 
then it runs off layer `z`. 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Useful Functions - QMK Firmware](https://docs.qmk.fm/#/ref_functions)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

レイヤーボタンの組み合わせだけでどうにかなる場合はこの関数を利用すると簡単。
書いてあるとおり、2つのレイヤーの状態をチェックし、両方とも有効の場合、3つ目のレイヤーを有効化する。

3つ目のレイヤーが最上位、つまり一番大きな数字が割り当てられているのが定石だが、
前2つより小さな数字を割り当て、透過しているキーの挙動を変更する、という使い方もできる。




## レイヤー切り替えボタンが複数ある場合の動作

左右にそれぞれレイヤー切り替えボタンがある時のレイヤー切り替えを考える。
普通に on/off するだけでは両方を押さえていた場合、片方を離してしまうだけでレイヤーが off になってしまう。

左右のキーの状態を保持しておく方法で解決した。

```c
void DualKeyPressed ( bool brother_state, uint8_t target ){

  if( brother_state ) return;

  layer_on( target );
  return;
}

void DualKeyReleased ( bool brother_state, uint8_t target1 ){ 

  if( brother_state ) return ;

  layer_off( target1 );
  return ;
}

bool LayerSwitch ( keyrecord_t *record, bool *key_state, bool brother, uint8_t layer ){ 
  if ( record -> event.pressed ) {
    *key_state = true;
    DualKeyPressed( brother, layer );
  } 
  else {
    *key_state = false;
    DualKeyReleased( brother, layer );
  }

  update_tri_layer(_LOWER, _RAISE, _NEUTRAL );
  return false;
}

...


  static bool l_lower = false;
  static bool r_lower = false;

  switch (keycode) {


//left lower setting
    case L_LOWER :
      return LayerSwitch( record, &l_lower, r_lower, _LOWER );
    break;

//right lower setting
    case R_LOWER : 
      return LayerSwitch( record, &r_lower, l_lower, _LOWER );
    break;

...
```

キー入力がなされた時点で自身の状態を記録、その後もう一方のキーの状態を調べ、押されていない場合は有効化、または無効化を行う。





## 複数のレイヤー切り替えボタン + Shiftみたいなキー

レイヤーを切り替えるボタンが押された上で、別のキーが入力されるとレイヤーが一段深くなるような挙動を実現するコードを、
このように実装した。

```c
void ComboPressed( bool brother_state ){

  if( brother_state ) return;

  if( layer_state_is( _LOWER ) ){
    layer_on( _LOWER_COMBO );
    return;
  }
  if( layer_state_is( _RAISE ) ){
    layer_on( _RAISE_COMBO );
    return ;
  }

}

void ComboReleased ( bool brother_state ){ 

  if( brother_state ) return;

  if( layer_state_is( _LOWER_COMBO ) ){ 
    layer_off( _LOWER_COMBO );
    return;
  }

  if( layer_state_is( _RAISE_COMBO ) ) { 
    layer_off( _RAISE_COMBO );
    return;
  }
}

void DualKeyPressed ( bool brother_state, uint8_t target ){

  if( brother_state ) return;

  layer_on( target );
  return;
}

bool DualKeyReleased ( bool brother_state, uint8_t target1, uint8_t target2 ){ 

  if( brother_state ) return false;

  layer_off( target1 );
  layer_off( target2 );

  return true;
}

bool process_record_user(uint16_t keycode, keyrecord_t *record) {

  static bool l_lower = false;
  static bool r_lower = false;

  static bool l_raise = false;
  static bool r_raise = false;

  static bool l_combo = false;
  static bool r_combo = false;

  switch (keycode) {


//left lower setting
    case L_LOWER :
      if ( record -> event.pressed ) {
        l_lower = true;
        DualKeyPressed( r_lower, _LOWER );
      } 
      else {

        l_lower = false;

        if ( DualKeyReleased( r_lower, _LOWER, _LOWER_COMBO ) ){ 
          l_combo = false;
          r_combo = false;
        }

      }
      return false;
    break;

//right lower setting
    case R_LOWER : 
      if ( record -> event.pressed ) {
        r_lower = true;
        DualKeyPressed( l_lower, _LOWER );
      } 
      else {

        r_lower = false;

        if( DualKeyReleased( l_lower, _LOWER, _LOWER_COMBO ) ){
          l_combo = false;
          r_combo = false;
        }

      }
      return false;
    break;

//left raise setting
    case L_RAISE :
      if ( record -> event.pressed ) {
        l_raise = true;
        DualKeyPressed( r_raise, _RAISE );
      } 
      else {

        l_raise = false;

        if ( DualKeyReleased( r_raise, _RAISE, _RAISE_COMBO ) ){ 
          l_combo = false;
          r_combo = false;
        }
      }
      return false;
    break;



//right raise setting
    case R_RAISE :
      if ( record -> event.pressed ) {
        r_raise = true;
        DualKeyPressed( l_raise, _RAISE );
      } 
      else {

        r_raise = false;

        if ( DualKeyReleased( l_raise, _RAISE, _RAISE_COMBO ) ){ 
          l_combo = false;
          r_combo = false;
        }
      }
      return false;
    break;


//left combo setting
    case L_COMBO :
      if( record -> event.pressed ){ 
        l_combo = true;
        ComboPressed( r_combo );
      }
      else{ 
        l_combo = false;
        ComboReleased( r_combo );
      }

      return false;
      break;

//right combo setting 
    case R_COMBO : 
      if( record -> event.pressed ) { 
        r_combo = true;
        ComboPressed( l_combo );
      }
      else{ 
        r_combo = false;
        ComboReleased( l_combo );
      }

      return false ;
      break;
        

  }
  return true;
}
```

全て組となったキーになっている。 Lower と Raise はキーマップの方で同時には押せないようにしている。
一段深い Lower Combo と Raise Combo レイヤーへの移行は同じ Combo と名付けたキーを利用する。

Lower/Raise
: 上記と同様、もう片方の状態をみてレイヤーの有効、無効の切り替えを行うと同時に、
レイヤーが解除された際、 Combo 状態だった場合は強制的に Combo を解除する。


Combo
: イベントが発生したタイミングで Lower/Raise どちらのレイヤーが有効になっているか判定して挙動を変化させている。





## Shift キーやレイヤーキーが押されたままになってしまう

{% capture text %}

以下に記載したmodifierキーがロックされる事象は、
[QMKのreadmeのPrevent stuck modifiers](https://github.com/qmk/qmk_firmware/blob/f39e1b5dfe7552f01dbc6eab95c268f41a9d98e2/readme.md#prevent-stuck-modifiers)
に記載されていることを
[みやおかさんから教えていただき](https://twitter.com/miyaoka/status/888608857454288897)、
その通りであることを確認しました。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[QMKでのレイヤー機能の挙動について - hrhg memo](http://hrhg.hatenablog.com/entry/2017/07/22/041303)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## Prevent stuck modifiers

Consider the following scenario:

1. Layer 0 has a key defined as Shift.
2. The same key is defined on layer 1 as the letter A.
3. User presses Shift.
4. User switches to layer 1 for whatever reason.
5. User releases Shift, or rather the letter A.
6. User switches back to layer 0.

Shift was actually never released and is still considered pressed.

If such situation bothers you add this to your `config.h`:

```c
#define PREVENT_STUCK_MODIFIERS
```

This option uses 5 bytes of memory per every 8 keys on the keyboard
rounded up (5 bits per key). For example on Planck (48 keys) it uses
(48/8)\*5 = 30 bytes.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[qmk_firmware/readme.md at f39e1b5dfe7552f01dbc6eab95c268f41a9d98e2 · qmk/qmk_firmware](https://github.com/qmk/qmk_firmware/blob/f39e1b5dfe7552f01dbc6eab95c268f41a9d98e2/readme.md#prevent-stuck-modifiers)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

```c
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
[_RAISE] = LAYOUT_ortho_4x12( \
  _______,  XXXXXXX,  KC_AMPR,  KC_LCBR,  KC_RCBR,  KC_PERC,  KC_BSLS,  KC_LBRC,  KC_RBRC,  KC_HASH,  XXXXXXX,  _______, \
  _______,  KC_TILD,  KC_PIPE,  KC_LPRN,  KC_RPRN,  KC_ASTR,  KC_SLSH,  KC_LABK,  KC_RABK,  KC_QUOT,  KC_COLN,  _______, \
  _______,  KC_GRV,   KC_CIRC,  KC_AT,    KC_DLR,   KC_PLUS,  KC_MINS,  KC_EQL,   KC_UNDS,  KC_DQUO,  KC_QUES,  _______, \
  _______,  _______,  _______,  _______,  _______,  _______,  _______,  _______,  _______,  _______,  _______,  _______  \
),
```

このキーマップの下部を

```c
  _______,  _______,  _______,  _______,  _______,  KC_A,     _______,  _______,  _______,  _______,  _______,  _______  \
```

```c
  _______,  _______,  _______,  _______,  _______,  _______,  _______,  KC_A,     _______,  _______,  _______,  _______  \
```

として実験した。

* 設定なし
    * Shift/Raise ともに押されたままになる
* PREVENT_STUCK_MODIFIERS
    * レイヤー移行したタイミングでリリースされる

ちゃんと機能していることがわかった。
容量に余裕があるのであればこの設定は入れたままのほうが良い気がする。

