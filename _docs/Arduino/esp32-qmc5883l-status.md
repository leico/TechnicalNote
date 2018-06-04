---
layout : post
title  : "QMC5883Lのステータス・レジスタを使ってみる"
date   : 2018/06/04
lastchange : 2018-06-04 22:58:03.
tags   :
  - esp32
  - I2C
  - HMC5883L
  - QMC5883L
  - Arduino
---

## ICの観測データの状況を把握する

[QMC5883Lでとりあえず3軸地磁気データを取得する]({{site.github.url}}{% link _docs/Arduino/esp32-qmc5883l-basic.md %})
で一応動作確認はできたが、実際問題どのように制御すればよいのか全くわからない。
今回はその謎の解決を図るべく、ステータス・レジスタの実験を行った。

参考:

* [3-Axis Magnetic Sensor QMC5883L](http://wiki.sunfounder.cc/images/7/72/QMC5883L-Datasheet-1.0.pdf)


## ステータスレジスタでデータの状況を把握する

{% capture text %}

### 7.2 Measurement Example

* Check status register _06H[0]_ , `1` means ready.
* Read data register _00H_ - _05H_


---

#### 9.2.2 Status Register
There are two status registers located in address _06H_ and _0CH_. 
Register _06H_ has three bits indicating for status flags, 
the rest are reserved for factory use. The status registers 
are read only bits. 

| Addr. |   7   |   6   |   5   |   4   |   3   |   2   |   1   |   0   | 
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
|   06H |  ---  |  ---  |  ---  |  ---  |  ---  |  DOR  |  OVL  |  DRDY |

Data Ready Register (DRDY), it is set when all threeaxis data is ready,
and loaded to the output data registers in the continuous measurement mode.
It is resetto `0` by reading any data register ( _00H_ - _05H_ ) 
through I2C commends

DRDY
: 
    `0` 
    : no new data

    `1`
    : new data is ready

Overflow flag(OVL) is set to `1` if any data of three axis magnetic sensor channels is
out of range. The output data of each axis saturates at 
-32768 and 32767, if any of the axis exceeds this range, OVL flag is set to `1`.
This flag is reset to `0` if next measurement goes back to the range of 
(-32768, 32767), otherwise, it keeps as `1`.

OVL
:   `0`
    : normal

    `1`
    : data overflow


Data Skip (DOR) bit is set to `1` if all the channels of output 
data registers are skipped in reading in the 
continuous-measurement mode. 
It is reset to `1` by reading any data register ( _00H_ - _05H_ ) through I2C

DOR
:   `0`
    : normal

    `1`
    : data skipped for read

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[3-Axis Magnetic Sensor QMC5883L](http://wiki.sunfounder.cc/images/7/72/QMC5883L-Datasheet-1.0.pdf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


データを読む際に status register で新しいデータが存在しているか確認してから読むように Example には書かれている。
その status register に関しては3つのフラグが存在しているらしい。

DRDY
: ビットが立っている場合、新しいデータがある

OVL
: ビットが立っている場合、データがオーバーフローした

DOR
: ビットが立っている場合、計測データがいくつかスキップされた

ということらしいぞ、というのが以下のソースコードを実行して判明した。
文章だけではいまいちよくわからない・・・。


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




// status register data
byte stat = 0;





void setup() {

  Wire.begin();
  
  Serial.begin(115200);
  while( ! Serial);
  
  Serial.println("QML5883L raw compass data");


  uint8_t err;
  Wire.beginTransmission(ADDRESS);
  Wire.write(0x0b);
  Wire.write(0x01);
  err = Wire.endTransmission();
  if(err){
    Serial.print("error Set/Reset period FBR : ");
    Serial.println(err);
  }

  Wire.beginTransmission(ADDRESS);
  Wire.write(0x09);
  Wire.write(0x1d);
  err = Wire.endTransmission();
  if(err){
    Serial.print("error set continuous measurement mode");
    Serial.println(err);
  }

  stat = 0;

}

void loop() {


  DATA x, y, z;

  byte err;
  std :: stringstream ss;







  //read status register
  Wire.beginTransmission(ADDRESS);
  Wire.write(0x06);
  err = Wire.endTransmission(false);

  if(err){
    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << ADDRESS;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << 0x06;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return;
  }

  Wire.requestFrom(ADDRESS, static_cast<uint8_t>(1));
  stat = Wire.read();
  Wire.endTransmission();

  Serial.print  ("status = ");
  Serial.println(stat);








  if( ! (stat & 0x07) ){
    Serial.println("Data is not ready");
    return;
  }

  if( stat & 0x04 ){
    Serial.println("Data skipped");
  }

  if( stat & 0x01 ){
    Serial.println("Data Ready");
  }


  err = readData(ADDRESS, X, x);
  if(err){

    ss << "X error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }

  err = readData(ADDRESS, Y, y);
  if(err){
    ss << "Y error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }

  err = readData(ADDRESS, Z, z);
  if(err){
    ss << "Z error : " << err;
    Serial.println( ss.str().c_str() );
    ss.str("");
  }


  //  printData("X", x);
  //  printData("Y", y);
  //  printData("Z", z);

  stat = 0;

}

void printData(const std :: string& _prefix, const DATA& _data){

  std :: stringstream ss;

  ss << _prefix << " MSB = 0x" << std :: hex << _data.msb;
  Serial.println( ss.str().c_str() );
  ss.str("");

  ss << _prefix << " LSB = 0x" << std :: hex << _data.lsb;
  Serial.println( ss.str().c_str() );
  ss.str("");


}


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

std :: ostream& operator<< (std :: ostream& out, const uint8_t value){
  out << static_cast<int>(value);
  return out;
}

```

出力結果

```
status = 1
Data Ready
status = 0
Data is not ready
status = 0
Data is not ready
status = 1
Data Ready
status = 0
Data is not ready
status = 1
Data Ready
status = 4
Data skipped
status = 0
Data is not ready
status = 1
Data Ready
status = 0
Data is not ready
status = 0
Data is not ready
status = 1
Data Ready
```

## ソースコードについて


汚いソースだが、やっていることはシンプルで、



```cpp
// status register data
byte stat = 0;
```



レジスタの値を取り込む変数を用意し




```cpp
  //read status register
  Wire.beginTransmission(ADDRESS);
  Wire.write(0x06);
  err = Wire.endTransmission(false);

  if(err){
    ss << "error : 0x" << std :: setfill('0') << std :: setw(2) << std :: hex << ADDRESS;
    ss << ":0x"        << std :: setfill('0') << std :: setw(2) << std :: hex << 0x06;
    ss << " -> "       << err;

    Serial.println( ss.str().c_str() );
    ss.str("");

    return;
  }

  Wire.requestFrom(ADDRESS, static_cast<uint8_t>(1));
  stat = Wire.read();
  Wire.endTransmission();

  Serial.print  ("status = ");
  Serial.println(stat);
```




レジスタのデータを取得して表示





```cpp
  if( ! (stat & 0x07) ){
    Serial.println("Data is not ready");
    return;
  }

  if( stat & 0x04 ){
    Serial.println("Data skipped");
  }

  if( stat & 0x01 ){
    Serial.println("Data Ready");
  }
```

各ビットのフラグの状況に合わせたエラー処理、メッセージ出力
ということを前回のソースに追加している。
