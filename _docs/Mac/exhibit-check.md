---
layout : post
title  : 作品展示の際の設定チェックシート
date   : 2017/08/14
lastchange : 2017-08-15 17:16:53.
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





## カレンダーを使って展示期間の自動終了をスケジューリングする

自動終了のみカレンダーから日時を設定できる。

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/04_calendar.png %}{% endcapture %}
{% assign caption = '

#### 開始

閉場後の時間に設定

#### 終了

開始で設定した時間+1分後

#### 繰り返し

会場が閉館する日時に合わせて繰り返すように設定

#### 繰り返し終了

展示終了日時まで繰り返す

#### 通知

`ファイルを開く`を指定して以下のApple Scriptをアプリケーションとして書き出したものを指定する

``` applescript
tell application "Finder"
  shut down
end tell
```

実行のタイミングは`イベントの開始時刻`にする。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}






## 自動ログインの設定

`システム環境設定` -> `ユーザとグループ` -> 左カラム下方 `ログインオプション`
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/05_login.png %}{% endcapture %}
{% assign caption = '

`自動ログイン`
: 展示用アカウントに設定。パスワードを求められるので入力する。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}








## プログラム自動起動設定

`システム環境設定` -> `ユーザとグループ` -> 左カラム `展示で使用するアカウント` -> `ログイン項目` タブ
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/06_autoexec.png %}{% endcapture %}
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
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/07_multiscreen.png %}{% endcapture %}
{% assign caption = '

`ディスプレイごとに個別の操作スペース`
: *OFF*

ログアウト後に適用される。
これをしないとシングルウィンドウでフルスクリーンになる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## Bluetooth Setup Assistantを起動させない

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/08_BSA.png %}{% endcapture %}
{% assign caption = '
キーボードやマウスを接続していないと、
デスクトップ用途のMacはBluetooth設定アシスタントが自動起動してしまう。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


`システム環境設定` -> `Bluetooth` -> 右下 `詳細設定`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/09_Bluetooth.png %}{% endcapture %}
{% assign caption = '
`起動時にキーボードが検出されない場合はBluetooth設定アシスタントを開く`
: *OFF*

`起動時にマウスまたはトラックパッドが検出されない場合はBluetooth設定アシスタントを開く`
: *OFF*

それぞれ確認のポップアップが出るので *OK* を押す。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## 通知オフ

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/10_Popup.png %}{% endcapture %}
{% assign caption = '
会期中にこんなの出て来られても、通知音が鳴ってくれても困る
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


`システム環境設定` -> `通知` 
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/11_NightTime.png %}{% endcapture %}
{% assign caption = '
おやすみモードを設定するとその期間は通知が出てこない。

`おやすみモードを入にする設定`
: *ON* 

展示開始前〜展示終了過ぎに設定する。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}







## 自動アップデートを切る


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/12_autoupdate.png %}{% endcapture %}
{% assign caption = '
ほんとやめてください。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`システム環境設定` -> `App Store`
{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/13_killupdate.png %}{% endcapture %}
{% assign caption = '
`アップデートを自動的に確認` 
: *OFF*
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}








## キーボードの修飾キーを切る

※キーボードによるインタラクションが展示内容に含まれる場合


`システム環境設定` -> `キーボード` -> 右下`修飾キー`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/14_killmodifies.png %}{% endcapture %}
{% assign caption = '

状況により

`Caps Lock`
: *OFF*

`Control`
: *OFF*

`Option`
: *OFF*

`Command`
: *OFF*
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}







## ペアレンタルコントロールを活用する

ペアレンタルコントロールを活用すると鑑賞者/学芸員の行動を制限することができる。

`システム環境設定` -> `ペアレンタルコントロール`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/15_controlapp.png %}{% endcapture %}
{% assign caption = '

### `アプリケーションタブ`

起動できるアプリケーションなどをコントロールすることができる

`このMac上のアプリケーションの使用を制限`
: *ON 一覧から展示に使うアプリケーションだけ選択する*


' | markdownify %}
{% include thumbnail.html url=url caption=caption %}






## 学芸員のユーザビリティ、万が一のために

### Dockの中身

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/16_dock.png %}{% endcapture %}
{% assign caption = '

アプリが再起動できるように利用するアプリへのエイリアスを作成しておく。
不必要なアプリは全て除外しておく。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



`システム環境設定` -> `ペアレンタルコントロール` -> `その他`タブ

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/17_lockdock.png %}{% endcapture %}
{% assign caption = '

展示用アカウントで運用する場合、Dock自体の変更ができないようにペアレンタルコントロールで制限をかけておく。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### 万が一アプリケーションが落ちた場合の保険


{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/18_blackout.png %}{% endcapture %}
{% assign caption = '

真っ黒でなにも表示されないようにする。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



デスクトップを空にする

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/19_datamove.png %}{% endcapture %}
{% assign caption = '

デスクトップのデータを全て別の場所に退避させる

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}




メニューバーを隠す

`システム環境設定` -> `一般`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/20_hidebar.png %}{% endcapture %}
{% assign caption = '


`メニューバーを自動的に隠す/表示`
: *ON*

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



Dockを隠す

`システム環境設定` -> `Dock`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/21_hidedock.png %}{% endcapture %}
{% assign caption = '


`Dockを自動的に隠す/表示`
: *ON*

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}



Finderの設定

`Finder` -> `環境設定`

{% capture url %}{{ site.github.url }}{% link _docs/Mac/images/exhibit-check/22_Finder.png %}{% endcapture %}
{% assign caption = '


`デスクトップに表示する項目`
: *OFF*

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

