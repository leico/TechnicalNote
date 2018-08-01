---
layout : post
title  : macOSでESP32-dev-moduleを実験する。
date   : 2018/05/02
lastchange : 2018-08-01 15:02:58.
tags   :
  - arduino
  - esp32
  - macOS
  - Sierra
  - USB
---

## macOSでの資料が少なかったのでまとめる

ESP32という高性能なArduinoのようなチップが生まれて、ネット上ではさまざまな人が動作させているみたいだが、
macOSでの開発に関して、あまり資料を探しても無かったので動作確認できるところまでをまとめる。

少し前までターミナルでの作業だったが、Arduino IDE から簡単にインストールできるようになった。
その過程にアップデートをしている。({{ page.lastchange }})

参考

* [Install failure · Issue #1656 · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/issues/1656)
* [arduino-esp32/boards_manager.md at master · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/boards_manager.md)
* [Getting Started with ESP32 Dev Module \| Random Nerd Tutorials](http://randomnerdtutorials.com/getting-started-with-esp32/)



## Arduino IDEをインストール

[Arduino - Software](https://www.arduino.cc/en/main/software)
からダウンロードして解凍する。解凍すると指定したディレクトリにArduino.appが出てくるのでApplicationsフォルダに移動してインストールする。


## Arduino core for the ESP32をインストール

{% capture text %}

You can now install using Arduino IDE board manager. 
The link is https://dl.espressif.com/dl/package_esp32_dev_index.json

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
by [lbernstone](https://github.com/lbernstone)
from [Install failure · Issue #1656 · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/issues/1656)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

* Start Arduino and open Preferences window.
* Enter `https://dl.espressif.com/dl/package_esp32_index.json` into Additional Board Manager URLs field. 
    You can add multiple URLs, separating them with commas.
* Open Boards Manager from Tools > Board menu and install _esp32_ platform 
    (and don't forget to select your ESP32 board from Tools > Board menu after installation).
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[arduino-esp32/boards_manager.md at master · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/boards_manager.md)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


最近は Arduino IDE 内の Board Manager を利用するのが一般的らしい。

Board Manager にリポジトリを登録するために、環境設定をする。
Arduino の _環境設定_ から _追加のボードマネージャのURL_ の設定を行う。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/01_Preference.png %}{% endcapture %}
{% capture caption %}
_Arduino -> Preferences..._ から環境設定を開く
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/02_BoardURL.png %}{% endcapture %}
{% capture caption %}
ボードのリポジトリを画面下方のテキストボックスに追加する。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/03_BoardURL2.png %}{% endcapture %}
{% capture caption %}
すでに何かを追加している場合は、テキストボックス右側のボタンでウィンドウを開く。

1行に1URLを記述する。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



リポジトリを追加したらボードマネージャを開いて ESP32 をインストールする。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/04_BoardManager.png %}{% endcapture %}
{% capture caption %}
_ツール -> ボード... -> ボードマネージャ_

を開く

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/05_ESP32.png %}{% endcapture %}
{% capture caption %}

一番下方に esp32 が追加されているので選択。

右下インストールボタンを押してインストール開始。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/06_Installing.png %}{% endcapture %}
{% capture caption %}

インストール中

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/07_After.png %}{% endcapture %}
{% capture caption %}

インストールが終わるとボード内に esp32 のメニューが追加される。

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




## ドライバをインストールする

接続したのだけれど、表示されないのでドライバを探す。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/08.png %}{% endcapture %}
{% assign caption = '
ESP32開発ボードをそのまま接続しただけでは認識されず、シリアルポートに表示されない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/09.png %}{% endcapture %}
{% assign caption = '
システム情報を見る限り接続はされている。そして Silicon Labratory のチップを使っているようだ。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どうやらドライバが入っていないのが問題のようだ。macOS用が配布されていて一安心。以下からmacOS用をダウンロードしてインストールする。
[USB - UART ブリッジ VCP ドライバ\|Silicon Labs](https://jp.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)

リンク切れの場合はCP2102のチップを元に検索する。

Arduino IDEを再起動してUSBポートでESP32が表示されるか確認する。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/10.png %}{% endcapture %}
{% assign caption = '
接続した時にUSBのシリアルポートが見れたらドライバのインストールが完了。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## 書き込み実験をする

基本設定は写真の通り。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/10.png %}{% endcapture %}
{% capture caption %}
| board            | ESP32 Dev Module        |
| Flash Mode       | QIO                     |
| Flash Frequency  | 80MHz                   |
| Flash Size       | 4MB(32Mb)               |
| Partition Scheme | Default                 |
| Upload Speed     | 115200                  |
| Core Debug Level | None                    |
| Serial Port      | /dev/cu.SLAB\_USBtoUART |
| Programmer       | USBasp                  |
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

まだいろいろ設定を詰める場面、方法があると思うがひとまず書き込みはこれでできることを確認する。
購入した時点で既にBlinkがインストールされているみたいだったので、何もしないプログラムで実験した。

```c
void setup(){
}

void loop(){
}
```

コンパイルして書き込みができるか確認する。

確認ができたらまたBlinkを書き込んでみるのだが、どのPIN番号がLEDにアサインされているのかを調べねばならない。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/11.jpg %}{% endcapture %}
{% capture caption %}
付属の仕様書から。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

GPIO2番のピンにLEDが並列接続されているようだが、GPIO2番が果たしていくつになるのかがわからない。

{% capture text %}
Note: the ESP32 pin assignment in the Arduino IDE works as expected. So, if you do something like this:

```c
pinMode(16, OUTPUT);
```

It refers to **GPIO16** .

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Getting Started with ESP32 Dev Module \| Random Nerd Tutorials](http://randomnerdtutorials.com/getting-started-with-esp32/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


これを信じる限り、GPIOの数字とピン番号は統一されているようだ。
で、あれば仕様書の通りであれば2番のポートにLEDが接続されていることになるのでやってみたら、正解だった。

```c
void setup() {
  // put your setup code here, to run once:

  pinMode(2, OUTPUT);

}

void loop() {
  // put your main code here, to run repeatedly:

  digitalWrite(2, HIGH);
  delay(1000);
  digitalWrite(2, LOW);
  delay(1000);

}
```

これで書き込みまではできるようになった。
