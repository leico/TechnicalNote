---
layout : post
title  : "レイヤー切り替えのキー設定について"
date   : 2018/08/28
lastchange : 2018-08-31 21:17:27.
tags   :
  - 自作キーボード
  - キーマップ
  - QMK firmware
  - layer
---

## レイヤーキーの挙動について少し調べた

キーマップをカスタマイズする際にレイヤーキーの挙動がきになったので調べてみた。

## 押しっぱなしの時、レイヤー切り替え先でキーが異なったらどうなるか

どういうことかというと、

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

この `LOWER` の位置に、_LOWER_ レイヤーで別のキーが割り当てられていたらどうなってしまうだろうか。

やってみた。

```c
/* Lower
 * ,-----------------------------------------------------------------------------------.
 * |   ~  |   !  |   @  |   #  |   $  |   %  |   ^  |   &  |   *  |   (  |   )  | Del  |
 * |------+------+------+------+------+-------------+------+------+------+------+------|
 * | Del  |  F1  |  F2  |  F3  |  F4  |  F5  |  F6  |   _  |   +  |      |   \  |  |   |
 * |------+------+------+------+------+------|------+------+------+------+------+------|
 * |RESET |  F7  |  F8  |  F9  |  F10 |  F11 |  F12 |ISO ~ |ISO | |      |      |Enter |
 * |------+------+------+------+------+------+------+------+------+------+------+------|
 * |      |      |      |      |Space |             |      | Next | Vol- | Vol+ | Play |
 * `-----------------------------------------------------------------------------------'
 */
[_LOWER] = LAYOUT_ortho_4x12( \
  KC_TILD, KC_EXLM, KC_AT,   KC_HASH, KC_DLR,  KC_PERC, KC_CIRC, KC_AMPR, KC_ASTR, KC_LPRN, KC_RPRN, KC_DEL, \
  KC_DEL,  KC_F1,   KC_F2,   KC_F3,   KC_F4,   KC_F5,   KC_F6,   KC_UNDS, KC_PLUS, KC_LCBR, KC_RCBR, KC_PIPE, \
  RESET,   KC_F7,   KC_F8,   KC_F9,   KC_F10,  KC_F11,  KC_F12,S(KC_NUHS),S(KC_NUBS),_______, _______, _______, \
  _______, _______, _______, _______, KC_SPC, _______, _______, _______, KC_MNXT, KC_VOLD, KC_VOLU, KC_MPLY \
)
```

`LOWER` に `KC_SPC` 、スペースキーを割り当ててみた。

これを書き込んでみる。

## 結果

```sh
$ !      0~%
```

_LOWER_ レイヤーから戻れなくなった。

## 分析


レイヤー切り替えのソースを見てみると

```sh
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

`event.pressed == true` でレイヤーON、`false`でレイヤーOFFになっている。
`LOWER` キーが _LOWER_ レイヤーの同じ位置に存在していたのであれば、

1. Lower キー押下
2. `event.pressed == true`
3. レイヤー切り替え
4. キー入力(Lowerレイヤー)
5. Lower キーリリース
6. `event.pressed == false`
7. レイヤー切り替え
8. キー入力(defaultレイヤー)

となるが、異なるキーがマッピングされている場合、

1. Lower キー押下
2. `event.pressed == true`
3. レイヤー切り替え
4. 元Lowerキーリリース(Lowerレイヤー)
5. `event.pressed == true`

とリリースしたイベントが発生せず、そのまま残ってしまうようだ。




## 解決策

偶然解決策をいろいろなことを調べている最中に発見した。
このページの一番最後に記載している。

[QMK : キーマップのカスタマイズ]({{site.github.url}}{% link _docs/QMK/key-customize.md %})


