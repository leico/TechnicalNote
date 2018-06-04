---
layout : post
title  : "QMC5883Lのファーストステップ"
date   : 2018/05/29
lastchange : 2018-06-04 17:42:12.
tags   :
  - esp32
  - I2C
  - HMC5883L
  - QMC5883L
  - Arduino
---

## このICってどうなっているのだ

[ESP32でI2C接続: QMC5883L(HMC5883L)を使ってみる]({{site.github.url}}{% link _docs/Arduino/esp32-qmc5883l.md %})でひとまずデータを取ることができるようになったのだが、
このICの仕組みはどうなっているのだろう。
ということで仕様書を見ながら実装をしてみる。
今回は仕様書の最初に載っている方法を実装する。

参考:

* [3-Axis Magnetic Sensor QMC5883L](http://wiki.sunfounder.cc/images/7/72/QMC5883L-Datasheet-1.0.pdf)

## 連続測定モード

{% capture text %}

### 7.1 Continuous Mode Setup Example

* Write Register 0BH by 0x01 (Define Set/Reset period)
* Write Register 09H by 0x1D (Define OSR = 512, Full Scale Range = 8Gauss,
ODR = 200Hz, set continuous measurement mode)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[3-Axis Magnetic Sensor QMC5883L](http://wiki.sunfounder.cc/images/7/72/QMC5883L-Datasheet-1.0.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

<table>
  <thead>
    <tr>
      <th>Addr.</th>
      <th>7</th>
      <th>6</th>
      <th>5</th>
      <th>4</th>
      <th>3</th>
      <th>2</th>
      <th>1</th>
      <th>0</th>
      <th>Access</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>00H</td>
      <td colspan="8">Data Output X LSB Register   XOUT[7:0]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>01H</td>
      <td colspan="8">Data Output X MSB Register   XOUT[15:8]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>02H</td>
      <td colspan="8">Data Output Y LSB Register   YOUT[7:0]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>03H</td>
      <td colspan="8">Data Output Y MSB Register   YOUT[15:8]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>04H</td>
      <td colspan="8">Data Output Z LSB Register   ZOUT[7:0]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>05H</td>
      <td colspan="8">Data Output Z MSB Register   ZOUT[15:8]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>06H</td>
      <td colspan="5"></td>
      <td>DOR</td>
      <td>OVL</td>
      <td>DRDY</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>07H</td>
      <td colspan="8">TOUT[7:0]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>08H</td>
      <td colspan="8">TOUT[15:8]</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>09H</td>
      <td colspan="2">OSR[1:0]</td>
      <td colspan="2">RNG[1:0]</td>
      <td colspan="2">ODR[1:0]</td>
      <td colspan="2">MODE[1:0]</td>
      <td>Read/Write</td>
    </tr>
    <tr>
      <td>0AH</td>
      <td>SOFT_RST</td>
      <td>ROL_PNT</td>
      <td colspan="5"></td>
      <td>INT_ENB</td>
      <td>R/W, Read only on blanks</td>
    </tr>
    <tr>
      <td>0BH</td>
      <td colspan="8">SET/RESET Period FBR [7:0]</td>
      <td>Read/Write</td>
    </tr>
    <tr>
      <td>0CH</td>
      <td colspan="8">Reserved</td>
      <td>Read only</td>
    </tr>
    <tr>
      <td>0DH</td>
      <td colspan="8">Chip ID</td>
      <td>Read only</td>
    </tr>
  </tbody>
</table>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[3-Axis Magnetic Sensor QMC5883L](http://wiki.sunfounder.cc/images/7/72/QMC5883L-Datasheet-1.0.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

Example として最初に載っていたのが Continuous Mode Setup Example だった。連続測定モードらしい。

この記述を見る限り、アドレス _0x0b_ に `0x01` を書き込み、
_0x09_ に `0x1d` を書き込んでしまえばとりあえずは動くらしい。
この設定がひとまず普通の動作を提供するのだろうか。

そしてレジスターのアドレス表を見ると
_0x01_ から _0x05_ までに計測データが入っているらしい。

上記の設定を書き込んだ後、ここを読み出すようにすれば方角によって値が変化するのが確認できるらしいので、
書き込んでから連続で読み込むまでを実装してみる。

```cpp

#include <Wire.h>
#include <string>
#include <sstream>
#include <iomanip>

const uint8_t ADDRESS = 0x0d;
const uint8_t X = 0x00;
const uint8_t Y = 0x02;
const uint8_t Z = 0x04;

typedef struct{
  byte msb;
  byte lsb;
} DATA;

std :: ostream& operator<< (std :: ostream& out, const uint8_t value);

inline const byte readData (const uint8_t _address, const uint8_t _register, DATA& _data);
inline void       printData(const std :: string& _prefix, const DATA& _data);


void setup() {

  Wire.begin();
  
  Serial.begin(115200);
  while( ! Serial);
  
  Serial.println("QML5883L raw compass data");



  // access 0x0b register and write 0x01
  uint8_t err;
  Wire.beginTransmission(ADDRESS);
  Wire.write(0x0b);
  Wire.write(0x01);
  err = Wire.endTransmission();
  if(err){
    Serial.print("error Set/Reset period FBR : ");
    Serial.println(err);
  }



  // access 0x09 register and write 0x1d
  Wire.beginTransmission(ADDRESS);
  Wire.write(0x09);
  Wire.write(0x1d);
  err = Wire.endTransmission();
  if(err){
    Serial.print("error set continuous measurement mode");
    Serial.println(err);
  }

}

void loop() {


  DATA x, y, z;

  byte err;
  std :: stringstream ss;

  // read X data
  err = readData(ADDRESS, X, x);
  if(err){

    ss << "X error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }

  // read Y data
  err = readData(ADDRESS, Y, y);
  if(err){
    ss << "Y error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }

  // read Z data
  err = readData(ADDRESS, Z, z);
  if(err){
    ss << "Z error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }


  // output measurement data
  printData("X", x);
  printData("Y", y);
  printData("Z", z);

  delay(2000);
}



//function for output measurement data
void printData(const std :: string& _prefix, const DATA& _data){

  std :: stringstream ss;

  ss << _prefix << " MSB = 0x" << std :: hex << _data.msb;
  Serial.println( ss.str().c_str() );
  ss.str("");

  ss << _prefix << " LSB = 0x" << std :: hex << _data.lsb;
  Serial.println( ss.str().c_str() );
  ss.str("");


}

//function for read measurement data
const byte readData(const uint8_t _address, const uint8_t _register, DATA& _data){


  byte err;

  std :: stringstream ss;

  //X LSB
  Wire.beginTransmission(_address);
  Wire.write(_register);
  err = Wire.endTransmission(false);

  if(err){

    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << _address;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << _register;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return err;
  }
  Wire.requestFrom(_address, static_cast<uint8_t>(1));
  _data.lsb = Wire.read();
  Wire.endTransmission();
 


   //X MSB
  Wire.beginTransmission(_address);
  Wire.write(_register + 1);
  err = Wire.endTransmission(false);

  if(err){

    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << _address;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << _register + 1;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return err;
  }

  Wire.requestFrom(_address, static_cast<uint8_t>(1) );
  _data.msb = Wire.read();
  Wire.endTransmission();

  return static_cast<byte>(0);

}


//extend std::stringstream operator for uint_8t
std :: ostream& operator<< (std :: ostream& out, const uint8_t value){
  out << static_cast<int>(value);
  return out;
}

```

いきなりとても長いが、ひとまず出力結果がこちら

```
QML5883L raw compass data
X MSB = 0x2
X LSB = 0x5
Y MSB = 0xff
Y LSB = 0x67
Z MSB = 0xfc
Z LSB = 0x10
X MSB = 0x1
X LSB = 0xca
Y MSB = 0xfd
Y LSB = 0x51
Z MSB = 0xfc
Z LSB = 0x8e
X MSB = 0x1
X LSB = 0xf
Y MSB = 0x0
Y LSB = 0xc3
Z MSB = 0xfc
Z LSB = 0x13
X MSB = 0xfd
X LSB = 0x89
Y MSB = 0xfe
Y LSB = 0xdd
Z MSB = 0xff
Z LSB = 0x8b
....
```

ぐるぐる回してみたが、ちゃんと反映されているようだ。


## ソースコードについて

```cpp
std :: ostream& operator<< (std :: ostream& out, const uint8_t value){
  out << static_cast<int>(value);
  return out;
}
```

`std::stringstream` が `uint8_t` をちゃんと扱ってくれないため、
`uint8_t` を `int` へキャストする様に `operator<<` をオーバーロードしている。


```cpp


//function for read measurement data
const byte readData(const uint8_t _address, const uint8_t _register, DATA& _data){

  //X LSB
  Wire.beginTransmission(_address);
  Wire.write(_register);
  err = Wire.endTransmission(false);

  if(err){

    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << _address;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << _register;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return err;
  }
  Wire.requestFrom(_address, static_cast<uint8_t>(1));
  _data.lsb = Wire.read();
  Wire.endTransmission();



   //X MSB
  Wire.beginTransmission(_address);
  Wire.write(_register + 1);
  err = Wire.endTransmission(false);

  if(err){

    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << _address;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << _register + 1;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return err;
  }

  Wire.requestFrom(_address, static_cast<uint8_t>(1) );
  _data.msb = Wire.read();
  Wire.endTransmission();

  return static_cast<byte>(0);
}
```

{% capture text %}

LSBとMSBという言葉は、ありがちですが、最後のＢを文脈によってバイト（Byte）かビット（Bit）か読み分ける必要があるようです。
ビットと読んだ場合、

MSB（Most Significant Bit）
: 最上位ビット

LSB（Least Significant Bit）
: 最下位ビット

ということになります。
1を1バイトで表現したとき、ビット列が以下のように並んだと考えると、

| (MSB) |   |   |   |   |   |   |   |   |   | (LSB) |
| ----- | - | - | - | - | - | - | - | - | - | ----- |
|       | 0 | 0 | 0 | 0 |   | 0 | 0 | 0 | 1 |       |

となります。上の桁の方がMSBです。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[LSBとMSB](http://www.geocities.co.jp/Hollywood/6872/note/lsbmsb.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このICの出力データは16bitだが、各レジスタは8bitしかない。
なので上位バイトと下位バイトをそれぞれ別のレジスタから取得する。
実際に利用する時は、別々に取得したあと上位バイトを8bit分左シフトしたものと
下位バイトを合算させて用いる。

今回のソースコードはただ単純に下位バイトと上位バイトをそれぞれ別に取得、出力している。
上位バイトは必ず `下位バイトのアドレス + 1` となっているので、`printData`内部はこの法則を利用して上位バイトを取得している。



```cpp
//function for output measurement data
void printData(const std :: string& _prefix, const DATA& _data){

  std :: stringstream ss;

  ss << _prefix << " MSB = 0x" << std :: hex << _data.msb;
  Serial.println( ss.str().c_str() );
  ss.str("");

  ss << _prefix << " LSB = 0x" << std :: hex << _data.lsb;
  Serial.println( ss.str().c_str() );
  ss.str("");


}
```

取得したデータを構造体から抽出して出力している。
今回、データを一時格納するために

```cpp
typedef struct{
  byte msb;
  byte lsb;
} DATA;
```

という構造体を用いてみた。
