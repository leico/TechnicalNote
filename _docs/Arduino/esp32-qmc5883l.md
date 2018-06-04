---
layout : post
title  : "ESP32でI2C接続: QMC5883L(HMC5883L)を使ってみる"
date   : 2018/05/29
lastchange : 2018-05-31 01:52:55.
tags   :
  - esp32
  - I2C
  - HMC5883L
  - QMC5883L
  - I2C Scanner
---

## 動作しないし、I2C のアドレスがおかしい

[ESP32でI2C接続: I2C Scanner]({{site.github.url}}{% link _docs/Arduino/esp32-i2cscan.md %})
で HMC5883L の I2Cアドレスを確認したところ、どうもアドレスが違う。

今回はこのチップでサンプルソースを動作するまでをまとめる。

参考:
* [Arduinoでi2c通信でセンサから値をとってみる - Qiita](https://qiita.com/hurusu1006/items/f493ee4eb9998d5bd740)
* [3軸地磁気センサー(HMC5883L)の使い方 \[Arduino\]](https://www.petitmonte.com/robot/howto_hmc5883l.html)
* [HMC5883Lディジタル３軸地磁気センサ](https://strawberry-linux.com/pub/hmc5883l-manual.pdf)
* [i2cアドレスが0x0dのHMC5883Lモジュールについて - Qiita](https://qiita.com/shigeru-yokochi/items/46eb23bd64bdf333f1ab)
* [wrong i2c address - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?p=1164521#p1164521)
* [QMC5883LコンパスモジュールとArduino基盤のプロジェクト « osoyoo.com](http://osoyoo.com/ja/2017/09/14/qmc5883l-electronic-compass-for-arduino/)





{% capture text %}

#### i2cのスレーブに書き込むときのWireのお作法

1. Wire.beginTransmission(addr) -> 接続するI2Cのモジュールを選択
2. Wire.write(reg)  -> I2Cのモジュールの書き込みレジスタを指定
3. Wire.write(value) -> 2.のレジスタにデータを書き込む
4. Wire.endTransmission() -> 送信を完了しI2Cバスを開放

#### i2cのスレーブから読み込むときのWireのお作法

1. Wire.beginTransmission(addr) -> 接続するIC2のモジュールを選択
2. Wire.write(reg) -> I2Cのモジュールの読み込みレジスタを指定
3. Wire.endTransmission(false) -> 送信完了するがコネクションは維持させる
4. Wire.requestFrom(adr, size) -> IC2のモジュールから
    2.で指定したアドレスから指定サイズ分のデータ取得を要求
5. Wire.read() -> 2.のレジスタからのデータを取得する。
    4.で指定したサイズ分繰り返しで取得可


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Arduinoでi2c通信でセンサから値をとってみる - Qiita](https://qiita.com/hurusu1006/items/f493ee4eb9998d5bd740)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

よって、先人が上げてくれているソースコードから読み解くに


{% capture text %}
```cpp
// デバイスアドレス(スレーブ)
uint8_t DEVICE_ADDRESS = 0x1E;  
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[3軸地磁気センサー(HMC5883L)の使い方 \[Arduino\]](https://www.petitmonte.com/robot/howto_hmc5883l.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

**0x1E** のアドレスであるのが適切らしい。他に



{% capture text %}
I2C スレーブアドレス

|          | 7(MSB) |  6  |  5  |  4  |  3  |  2  |  1  | 0(LSB) |
| -------- | ------ | --- | --- | --- | --- | --- | --- | ------ |
| HMC5883L | 0      | 0   | 1   | 1   | 1   | 1   | 0   | R/W    |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HMC5883Lディジタル３軸地磁気センサ](https://strawberry-linux.com/pub/hmc5883l-manual.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

マニュアルからも **0b0011110** 、つまり **0x1E** であることがわかる。

だが 
```
Scanning...
I2C device found at address 0x0D  !
done
```
アドレスを取得してみると **0x0D** になっている。


{% capture text %}

センサとの通信がうまくいっているか確認するために、最初は _Identification Register_
を読み込んでみてください。**0x48** , **0x34** , **0x33** 場合
は通信に失敗していますのでスレーブアドレス、
配線・プルアップ・I2Cのプロトコルなどを確認してください。
I2Cバスの最大周波数は400kHzです。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[HMC5883Lディジタル３軸地磁気センサ](https://strawberry-linux.com/pub/hmc5883l-manual.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


```cpp
#include <Wire.h>



const uint8_t ADDRESS  = 0x0d;
const uint8_t IDENTITY = 0x48;


void setup() {

  Wire.begin();
  
  Serial.begin(115200);
  while( ! Serial);
  Serial.println("QMC5883L Identification Register check");

}

void loop() {

  delay(5000);

  byte ans;
  int8_t data = 0;

  Wire.beginTransmission(ADDRESS);
  Wire.write(IDENTITY);
  ans = Wire.endTransmission();

  if(ans){
    Serial.print("Identity Error: ");
    Serial.println(ans);
    return;
  }

  Wire.beginTransmission(ADDRESS);

  ans = Wire.requestFrom(ADDRESS, (uint8_t)1);

  Serial.print("Identity request from : ");
  Serial.println(ans);

  while( ! Wire.available());

  data = Wire.read();
  
  Serial.println("data : ");
  Serial.println(data, BIN);
  Serial.println(data, HEX);


  Wire.endTransmission();
}
```

出力結果

```
Identity request from : 1
data : 
11111111111111111111111111111111
FFFFFFFF
```

**0x48** を読み取っても、よくわからないものが出力されるだけだ。
何かがおかしい。

## 類似品のQMC5883Lである可能性

{% capture text %}
# 内容

手元にあるHMC588Lモジュール(3軸デジタルコンパス)が全く動作しない。
違う石を実装しているようだ。
結構出回っていると思うので、もし入手してしまった場合は本記事を参考にして頂ければ思い投稿します。

この記事の通りだった↓

<https://www.raspberrypi.org/forums/viewtopic.php?t=172244&p=1102197>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[i2cアドレスが0x0dのHMC5883Lモジュールについて - Qiita](https://qiita.com/shigeru-yokochi/items/46eb23bd64bdf333f1ab)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


リンク先に目を通すと


{% capture text %}
I've just bought of few of these and found the '5883' IC is a QMC5883 
which has different registers. The datasheet is here:

<https://github.com/luckypm/commn-informations/blob/master/%E5%9C%B0%E7%A3%81%E6%96%87%E6%A1%A3/%E8%88%AA%E7%BA%AC5983%E6%9B%BF%E4%BB%A3%E6%96%99%E8%B5%84%E6%96%99/QMC5883L%20Datasheet%201.0%20.pdf>

Working Arduino code is here:

<https://github.com/mechasolution/Mecha_QMC5883>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [dpouw](https://www.raspberrypi.org/forums/memberlist.php?mode=viewprofile&u=233937)
from [wrong i2c address - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?p=1164521#p1164521)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


積んでいるチップが QHC5883L の可能性が出てきた。


## QHC5883Lのサンプルコードを動かす

QHC5883Lで検索をかけたところ、以下のページが出てきた。

{% capture text %}

1. I2Cポートを使って、QMC5883LコンパスモジュールとArduino基盤を接続してください。
    QMC5883Lコンパスモジュール水平の状態に置いてください。
2. 次のリンクでライブラリをダウンロードして: 
    [QMC5883L Compass library](http://osoyoo.com/driver/DFRobot_QMC5883.zip).
    ダウンロードしたのZIPファイルを解凍して、Arduino IDE のフォルダ 
    _..\arduino-1.x.x\libraries_ に置いてくださいませ。

3. Arduino IDEに: _File -> Example -> DFRobot\_QMC5883\QMC5883\_compass_ を選択してください。
4. 選択したのコードのアップロードしてください。
5. Arduino IDEにserial monitor窓を開けて、
    獲得したのデータをご確認よろしくお願い致します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[QMC5883LコンパスモジュールとArduino基盤のプロジェクト « osoyoo.com](http://osoyoo.com/ja/2017/09/14/qmc5883l-electronic-compass-for-arduino/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


この流れに沿ってサンプルを動かしてみるが・・・

```
Initialize QMC5883
Guru Meditation Error: Core  1 panic'ed (IntegerDivideByZero)
. Exception was unhandled.
Core 1 register dump:
PC      : 0x400e743b  PS      : 0x00060730  A0      : 0x800d1358  A1      : 0x3ffca430  
A2      : 0x00000000  A3      : 0xfffff1f2  A4      : 0x00000000  A5      : 0xfffffe98  
A6      : 0x000002d0  A7      : 0x00000000  A8      : 0x800d121d  A9      : 0x3ffca410  
A10     : 0x00000000  A11     : 0x0000000d  A12     : 0x00000002  A13     : 0x00000001  
A14     : 0x00060723  A15     : 0xff000000  SAR     : 0x0000000a  EXCCAUSE: 0x00000006  
EXCVADDR: 0x00000000  LBEG    : 0x400e73c8  LEND    : 0x400e73d7  LCOUNT  : 0x00000000  

Backtrace: 0x400e743b:0x3ffca430 0x400d1355:0x3ffca450 0x400d0a96:0x3ffca490 0x400e5309:0x3ffca4d0

Rebooting...
```

途中でESP32がカーネルパニックを起こして動作しない。

ソースコードを追ったところ、

```cpp
Vector norm = compass.readNormalize();
```

ここで止まることが判明した。
もうひとつRawデータを扱うサンプルがあるのでこちらを試してみる。

```
isHMC_= 0
isQMC_= 1
Initialize QMC5883
-1540.00:4340.00:12796.00
-1540.00:4340.00:12800.00
-1540.00:4340.00:12796.00
-1432.00:4250.00:12800.00
-1420.00:4240.00:12796.00
-1336.00:4366.00:12800.00
-1300.00:4420.00:12796.00
-1516.00:4486.00:12800.00
-1660.00:4530.00:12796.00
...
-1560.00:4290.00:12800.00
-1570.00:4396.00:12800.00
-1510.00:4480.00:12796.00
-1492.00:4472.00:12800.00
-1438.00:4282.00:12800.00
-1264.00:3659.00:12800.00
950.00:3740.00:12796.00
950.00:3740.00:12800.00
950.00:3740.00:12796.00
950.00:3740.00:12796.00
950.00:3740.00:12796.00
950.00:3740.00:12796.00
950.00:3740.00:12796.00
-412.00:3896.00:12800.00
-1320.00:4000.00:12796.00
-1320.00:4000.00:12796.00
-1320.00:4000.00:12796.00
-1401.00:4096.00:12800.00
-1590.00:4320.00:12796.00
-1576.00:4315.00:12800.00
-1450.00:4270.00:12796.00
-1450.00:4270.00:12800.00
-1446.00:4352.00:12800.00
...
-1510.00:4500.00:12796.00
-1406.00:4332.00:12800.00
-1238.00:4156.00:12800.00
-1280.00:4321.00:12800.00
-1420.00:4440.00:12796.00
-1420.00:4440.00:12796.00
-1420.00:4440.00:12796.00
-1420.00:4440.00:12800.00
1420.00:3820.00:12800.00
1420.00:3820.00:12796.00
1420.00:3820.00:12796.00
```

どうやらこちらは動作した。
そしてやはりこのチップはQMC5887Lチップのようだ。
