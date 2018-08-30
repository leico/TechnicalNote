---
layout : post
title  : "QMK : キーマップのカスタマイズ"
date   : 2018/08/28
lastchange : 2018-08-31 04:06:47.
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

という書式で指定してあげればよい。


## レイヤーのスイッチングに関して


