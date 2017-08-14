---
layout : post
title  : 作品展示の際の設定チェックシート
date   : 2017/08/14
lastchange : 2017-08-14 12:21:32.
tags   :
  - Mac
  - Macbook
  - OSX
  - macOS
  - exhibition
  - system preference
---

## いろんなところに設定しておかねばならないものが離散している

絶対忘れるのでメモを残しておく。





## スクリーンセーバをオフにする

`システム環境設定` -> `デスクトップとスクリーンセーバ` -> `スクリーンセーバ` タブ
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/01_screensaver.png %}{% endcapture %}
{% assign caption = '
`開始までの時間`
: *開始しない* 

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}








## ディスプレイ、HDDスリープなどの設定

`システム環境設定` -> `省エネルギー` -> `電源アダプタ` タブ

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/02_energy.png %}{% endcapture %}
{% assign caption='

`ディスプレイをオフにするまでの時間`
: *しない*

---

状況により

`ディスプレイがオフの時コンピュータを自動でスリープさせない`
: *ON*

`可能な場合はハードディスクをスリープさせる`
: *OFF*

`Wi-Fiネットワークアクセスによるスリープ解除`
: *OFF*

`電源アダプタ接続中にPower Napをオンにする`
: *OFF*

---

グラボ2つ搭載している場合、画面一番上

`グラフィックスの自動切り替え`
: *OFF*
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}






## 自動起動の設定

`システム環境設定` -> `デスクトップとスクリーンセーバ` -> 右下 `スケジュール`
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/03_schedule.png %}{% endcapture %}
{% assign caption = '
`起動またはスリープ解除`、`システム終了`の時刻を設定する。

*毎日*、*毎週*、*平日*、あるいは特定の曜日が選択できる。
*火曜日以外* みたいな設定はできない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}







## 自動ログインの設定

`システム環境設定` -> `ユーザとグループ` -> 左カラム下方 `ログインオプション`
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/04_login.png %}{% endcapture %}
{% assign caption = '

`自動ログイン`
: 展示用アカウントに設定。パスワードを求められるので入力する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}








## 自動起動設定

`システム環境設定` -> `ユーザとグループ` -> 左カラム `展示で使用するアカウント` -> `ログイン項目` タブ
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/05_autoexec.png %}{% endcapture %}
{% assign caption = '

実行するプログラムをドラッグ&ドロップ。

起動直後に実行されるとコンピュータが外部デバイスを認識する前に走ってしまうことがあるので
1分ほど経ってから起動するAppleScriptを実行、AppleScript経由でプログラムを実行させている。

``` applescript
delay 60
tell application "/Users/user/Desktop/program/program.app" 
to activate
```

これを書き換え、アプリケーションとして書き出し後、自動起動に登録。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}





## マルチウィンドウ設定

`システム環境設定` -> `Mission Control`
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/06_multiscreen.png %}{% endcapture %}
{% assign caption = '

`ディスプレイごとに個別の操作スペース`
: *OFF*

ログアウト後に適用される。
これをしないとシングルウィンドウでフルスクリーンになる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## Bluetooth Setup Assistantを起動させない

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/07_BSA.png %}{% endcapture %}
{% assign caption = '
キーボードやマウスを接続していないと、
デスクトップ用途のMacはBluetooth設定アシスタントが自動起動してしまう。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


`システム環境設定` -> `Bluetooth` -> 右下 `詳細設定`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/08_Bluetooth.png %}{% endcapture %}
{% assign caption = '
`起動時にキーボードが検出されない場合はBluetooth設定アシスタントを開く`
: *OFF*

`起動時にマウスまたはトラックパッドが検出されない場合はBluetooth設定アシスタントを開く`
: *OFF*

それぞれ確認のポップアップが出るので *OK* を押す。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## 通知オフ

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/09_Popup.png %}{% endcapture %}
{% assign caption = '
会期中にこんなの出て来られても、通知音が鳴ってくれても困る
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


`システム環境設定` -> `通知` 
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/10_NightTime.png %}{% endcapture %}
{% assign caption = '
おやすみモードを設定するとその期間は通知が出てこない。

`おやすみモードを入にする設定`
: *ON* 

展示開始前〜展示終了過ぎに設定する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

