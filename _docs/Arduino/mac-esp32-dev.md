---
layout : post
title  : macOSでESP32-dev-moduleを実験する。
date   : 2018/05/02
lastchange : 2018-05-02 04:54:19.
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

[Arduino core for the ESP32 のインストール方法 \| mgo-tec電子工作](https://www.mgo-tec.com/arduino-core-esp32-install)

基本この流れに沿って進めてゆくが、所々で問題が発生する。

参考

* [Arduino core for the ESP32 のインストール方法 \| mgo-tec電子工作](https://www.mgo-tec.com/arduino-core-esp32-install)
* [Arduino - Software](https://www.arduino.cc/en/main/software)
* [espressif/arduino-esp32: Arduino core for the ESP32](https://github.com/espressif/arduino-esp32)
* [arduino-esp32/mac.md at master · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/mac.md)
* [Mac Install doesn't work - sript errors Issue #1336](https://github.com/espressif/arduino-esp32/issues/1336)
* [Installation failling on macos · Issue #1143](https://github.com/espressif/arduino-esp32/issues/1143#issuecomment-381772594)
* [Releases · igrr/mkspiffs · GitHub](https://github.com/igrr/mkspiffs/releases)
* [USB - UART ブリッジ VCP ドライバ\|Silicon Labs](https://jp.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)
* [Getting Started with ESP32 Dev Module \| Random Nerd Tutorials](http://randomnerdtutorials.com/getting-started-with-esp32/)



## Arduino IDEをインストール

[Arduino - Software](https://www.arduino.cc/en/main/software)
からダウンロードして解凍する。解凍すると指定したディレクトリにArduino.appが出てくるのでApplicationsフォルダに移動してインストールする。


## Arduino core for the ESP32をインストール

[espressif/arduino-esp32: Arduino core for the ESP32](https://github.com/espressif/arduino-esp32)
からArduino core for the ESP32をインストールする。
インストールには以下のコマンドラインを実行した。


{% capture text %}

Installation instructions for Mac OS

1. Install latest Arduino IDE from arduino.cc

2. Open Terminal and execute the following command (copy->paste and hit enter):

```sh
mkdir -p ~/Documents/Arduino/hardware/espressif && \
cd ~/Documents/Arduino/hardware/espressif && \
git clone https://github.com/espressif/arduino-esp32.git esp32 && \
cd esp32 && \
git submodule update --init --recursive && \
cd tools && \
python get.py
```

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[arduino-esp32/mac.md at master · espressif/arduino-esp32](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/mac.md)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


コマンドラインを見る限り、_Documents/Arduino/hardware_ 内部にArduino IDEに追加するハードウェアのプラグインを入れてゆくようだ。
しかし、このコマンドラインがちゃんと通らない。

{% capture text %}

```sh
Cloning into 'esp32'...
remote: Counting objects: 7733, done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 7733 (delta 1), reused 4 (delta 0), pack-reused 7720
Receiving objects: 100% (7733/7733), 114.36 MiB | 4.56 MiB/s, done.
Resolving deltas: 100% (4496/4496), done.
Submodule 'libraries/BLE' (https://github.com/nkolban/ESP32_BLE_Arduino.git) registered for path 'libraries/BLE'
Cloning into '/Users/michael/Documents/Arduino/hardware/espressif/esp32/libraries/BLE'...
Submodule path 'libraries/BLE': checked out 'af865a916795289c8e7e09b091ff2140c33fc3fe'
System: Darwin, Info: Darwin-17.5.0-x86_64-i386-64bit
Platform: x86_64-apple-darwin
Downloading xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz
Done
Extracting xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz
Downloading esptool-da31d9d-macos.tar.gz
Done
Extracting esptool-da31d9d-macos.tar.gz
Downloading mkspiffs-0.2.2-arduino-esp32-osx.tar.gz
Traceback (most recent call last):
File "get.py", line 148, in
get_tool(tool)
File "get.py", line 103, in get_tool
urlretrieve(url, local_path, report_progress)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/urllib.py", line 98, in urlretrieve
return opener.retrieve(url, filename, reporthook, data)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/urllib.py", line 245, in retrieve
fp = self.open(url, data)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/urllib.py", line 213, in open
return getattr(self, name)(url)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/urllib.py", line 443, in open_https
h.endheaders(data)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/httplib.py", line 1038, in endheaders
self._send_output(message_body)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/httplib.py", line 882, in _send_output
self.send(msg)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/httplib.py", line 844, in send
self.connect()
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/httplib.py", line 1263, in connect
server_hostname=server_hostname)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/ssl.py", line 363, in wrap_socket
_context=self)
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/ssl.py", line 611, in init
self.do_handshake()
File "/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/ssl.py", line 840, in do_handshake
self._sslobj.do_handshake()
IOError: [Errno socket error] [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:661)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [esp32fqn](https://github.com/esp32fqn) from [Mac Install doesn't work - sript errors Issue #1336](https://github.com/espressif/arduino-esp32/issues/1336)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これと同様のエラーが出て止まってしまう。このエラーを以下を参考に解決した。

{% capture text %}
Manually fetching mkspiffs-0.2.2-arduino-esp32-osx.tar.gz into hardware/espressif/esp32/tools/dist and then re-running get.py worked for me.

Note that the root cause of the problem here is that the python included with OSX versions 10.12 (Sierra) and earlier only supports OpenSSL version 0.9.8,
and that only supports TLS 1.1. Recently github moved to requiring TLS 1.2,
and so get.py fails when fetching mkspiffs. It's okay fetching one or two packages manually,
but over the long term I expect more and more sites will require TLS 1.2,
so manual fetches will become tedious and sooner or later OSX users will either need to upgrade to High Sierra 
or install a new python and openssl 1.0.2. 
(Apple replaced OpenSSL 0.9.8 with LibreSSL 1.x.x in newer versions of OSX to address this problem.)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [tferrin (Tom Ferrin)](https://github.com/tferrin) from [Installation failling on macos · Issue #1143](https://github.com/espressif/arduino-esp32/issues/1143#issuecomment-381772594)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

手動で **mkpiss-0.2.2-arduino-esp32-osx.tar.gz** をインストール先のディレクトリ、 _Documents/Arduino/hardware/espressif/esp32/tools/dist/_ に入れてしまえば、
ダウンロード完了していると判断してインストールが先に進むようだ。

なので、以下から **mkpiss-0.2.2-arduino-esp32-osx.tar.gz** をダウンロードしてインストールを先に進める。

[Releases · igrr/mkspiffs · GitHub](https://github.com/igrr/mkspiffs/releases)

インストールするVer.はその都度、 **get.py** のログ出力から追うのが賢明。

```sh
python get.py

System: Darwin, Info: Darwin-16.7.0-x86_64-i386-64bit
Platform: x86_64-apple-darwin
Tool xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz already downloaded
Extracting xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz
Tool esptool-da31d9d-macos.tar.gz already downloaded
Extracting esptool-da31d9d-macos.tar.gz
Tool mkspiffs-0.2.2-arduino-esp32-osx.tar.gz already downloaded
Extracting mkspiffs-0.2.2-arduino-esp32-osx.tar.gz
Renaming mkspiffs-0.2.2-arduino-esp32-osx to mkspiffs
Done
```

正常終了するとこのようなログが出る。

## ドライバをインストールする

接続したのだけれど、表示されないのでドライバを探す。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/01.png %}{% endcapture %}
{% assign caption = '
ESP32開発ボードをそのまま接続しただけでは認識されず、シリアルポートに表示されない。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/02.png %}{% endcapture %}
{% assign caption = '
システム情報を見る限り接続はされている。そして Silicon Labratory のチップを使っているようだ。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

どうやらドライバが入っていないのが問題のようだ。macOS用が配布されていて一安心。以下からmacOS用をダウンロードしてインストールする。
[USB - UART ブリッジ VCP ドライバ\|Silicon Labs](https://jp.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)

リンク切れの場合はCP2102のチップを元に検索する。

Arduino IDEを再起動してUSBポートでESP32が表示されるか確認する。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/03.png %}{% endcapture %}
{% assign caption = '
接続した時にUSBのシリアルポートが見れたらドライバのインストールが完了。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## 書き込み実験をする

基本設定は写真の通り。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/03.png %}{% endcapture %}
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

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/mac-esp32-dev/04.jpg %}{% endcapture %}
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
