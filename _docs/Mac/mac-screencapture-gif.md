---
layout : post
title  : Macでキーボード入力を表示させながらスクリーンキャプチャを行う
date   : 2018/07/09
lastchange : 2018-07-09 18:50:21.
tags   :
  - Mac
  - High Sierra
  - Application
  - アプリケーション
  - KeyCast
  - LICEcap
---

## 他人に状況説明をするのに手軽な方法がほしい

キー入力の内容が表示されながらスクリーン録画されているgifって
どうやって作られているのだろうと思ったので調べた。

参考:

* [キー入力を表示しながらスクリーンショットGifを撮る](https://rcmdnk.com/blog/2014/08/01/computer-mac-windows/#keycast)
* [Macの画面をGIFアニメとしてキャプチャできる３アプリを比較検討してみた \| あなたのスイッチを押すブログ](http://bamka.info/mac-gif-captcha)
* [Releases · cho45/KeyCast](https://github.com/cho45/KeyCast/releases)
* [Cockos Incorporated \| LICEcap](https://www.cockos.com/licecap/)
* [32 ビット App と macOS High Sierra 10.13.4 の互換性 - Apple サポート](https://support.apple.com/ja-jp/ht208436)



## キー入力に KeyCastを使う

{% capture text %}

### KeyCast

**追記: 2015/03/14**

* [cho45/KeyCast: Record keystroke for screencast](https://github.com/cho45/KeyCast)


最近出来たGitHub上で公開されてるアプリ。

見た目とかは **KeyCastr** (一文字違い)の **Default** に似た感じです。

こちらではパスワード欄への入力時や `sudo` 時に自動で 表示を停止する機能や
AppleScriptからStart/Stopする機能が付いています。

その辺り、 **KeyCastr** よりも高機能で、 新しくてメンテされてるのでこれに移行しても良さげ。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[キー入力を表示しながらスクリーンショットGifを撮る](https://rcmdnk.com/blog/2014/08/01/computer-mac-windows/#keycast)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ダウンロードはこちら

* [Releases · cho45/KeyCast](https://github.com/cho45/KeyCast/releases)

実際にこれを使ってみた動画が github に挙がっている。

<iframe width="560" height="315" 
src="https://www.youtube.com/embed/RBCT56Tzpu4" frameborder="0" 
allow="autoplay; encrypted-media" allowfullscreen>
</iframe>

動画で紹介されていないのでざっくりと説明。




まず Applications ディレクトリに入れて最初に起動した時、以下のようなダイアログが出るので
右側の _Open System Preference_ をクリックしてシステム環境設定を開く。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/01_dialog.png %}{% endcapture %}
{% capture caption %}
最初に現れるダイアログ
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}







_KeyCast.app_ が `システム環境設定 -> セキュリティとプライバシー -> プライバシー` の中に存在するので、
ここでコンピュータの制御を許可する。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/02_Accessibility.png %}{% endcapture %}
{% capture caption %}

ダイアログで _Open System Preference_ を選択するとこの画面になる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



再度 _KeyCast.app_ を開くとメニューバー右上に _KeyCast_ が現れる。
メニューから

* 有効化/無効化
* 表示されている文字の消去
* 設定

などを行うことができる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/03_menubar.png %}{% endcapture %}
{% capture caption %}

右上で _KeyCast.app_ の制御を行う。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



本体は初期状態では右下に現れる。
入力したキーが次々と表示され、スクロールしてゆく。
一番下が最新のもの。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/04_KeyCast.png %}{% endcapture %}
{% capture caption %}

入力キーが表示される。文字をドラッグすることで位置を変更できる。


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


設定ではキー表示を行うウィンドウサイズ、表示行数、文字の透明度、フォントの種類などが設定できる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/05_Setting.png %}{% endcapture %}
{% capture caption %}
設定画面

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


それなりにシンプルなので使い方に迷うこともないでしょう。
開発が3年前くらいから止まっているのが気がかりだが、
githubに挙がっているので誰かが開発継続してくれることを期待。



## Gifアニメーションでスクリーンを録画する

{% capture text %}

## 最終的に「LICEcap」

最終的に気に入ったのが「LICEcap」というフリーのアプリ。
[先日ごりゅごさんもご自身のブログで紹介](http://goryugo.com/20150819/screen_capture_on_gif/)
されていました。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Macの画面をGIFアニメとしてキャプチャできる３アプリを比較検討してみた \| あなたのスイッチを押すブログ](http://bamka.info/mac-gif-captcha)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

ちなみにGifのキャプチャは 
[LICEcap](https://www.cockos.com/licecap/)
を使っています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[キー入力を表示しながらスクリーンショットGifを撮る](https://rcmdnk.com/blog/2014/08/01/computer-mac-windows/#keycast)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

LICEcap というのが定番らしい。これをインストールして使ってみる。

ダウンロードは以下

* [Cockos Incorporated \| LICEcap](https://www.cockos.com/licecap/)


ライセンスに同意することが求められるのでよく読んで同意する。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/06_LISEcastagree.png %}{% endcapture %}
{% capture caption %}

GNU General Public License なのでそちらを検索してもらってもよい。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


macOS のバージョンによっては、
起動すると **お使いのMac用に最適化されていません** 
といったようなダイアログが表示されることがある。

* [32 ビット App と macOS High Sierra 10.13.4 の互換性 - Apple サポート](https://support.apple.com/ja-jp/ht208436)


警告はされるが現状動くのでアップデートを待ちつつ使おう。
起動するとこのようなウィンドウの枠が現れる。
この枠の中が録画されて gif アニメーションになる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/07_LISEWindow.png %}{% endcapture %}
{% capture caption %}

背景が透明なウィンドウ

ウィンドウ右下のダイアログで

* FPS
* width
* height

の設定ができる。
_width_ , _height_ に関してはウィンドウサイズを操作することによる変更も可能。

ウィンドウ左下の _Record_ で録画開始、 _Stop_ で終了する。
録画中 は _Record_ が _Pause_ になるので途中で中断する事もできる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}






_Record_ を押すとファイル保存用ダイアログが出てくる。


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/08_Record.png %}{% endcapture %}
{% capture caption %}

ここで

* 先頭にタイトルを入れる
* 秒数を表示する
* マウスクリックで丸を描画

等の設定ができる。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


この組み合わせでこんな感じのものができあがる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/mac-screencapture-gif/09_sample.gif %}{% endcapture %}
{% capture caption %}

動作検証中のサンプル

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

