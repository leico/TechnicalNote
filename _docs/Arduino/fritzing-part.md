---
layout : post
title  : Fritzing用のパーツをIllustratorで作成する
date   : 2018/05/25
lastchange : 2018-05-25 02:33:39.
tags   :
  - fritzing
  - part
  - Illustrator
  - svg
---

## Inkscapeの記事はそれなりにヒットするが・・・

Illustratorに関してはあんまり出てこないので、
作った時に参考にしたサイトをまとめつつ書いてみる。

参考:

* [Fritzingパーツ作成方法 \| Home Made Garbage](http://homemadegarbage.0t0.jp/fritzing1)
* [Fritzing カスタムパーツの作り方 – jumbleat](https://jumbleat.com/2017/01/18/making_custom_parts_fritzing/#1)
* [Ｆｒｉｔｚｉｎｇ：パーツ作成１](http://www.geocities.jp/zattouka/GarageHouse/Tool/Fritzing/MakeParts/MakeParts.htm)
* [Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
* [Fritzing - Download fonts and templates](http://fritzing.org/fritzings-graphic-standards/download-fonts-and-templates)
* [Make Your Own Fritzing Parts - learn.sparkfun.com](https://learn.sparkfun.com/tutorials/make-your-own-fritzing-parts#metadata)

## 作り方は載っているが理由が見当たらない

いろんなサイトに作り方が書かれているけれども、曲りなりにも設計図、
回路図というものが関係してくる。デザインするにも寸法の仕様だとか、
色であるとかが多少は指定されているはずである。

しかし、寸法とか記述の方法とかの根拠がいまいちはっきりしないと思っていたら、
本家のサイトに基本仕様が書かれていた。

* [Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)

## 基本ルール

Fritzingで回路図を作るためには3つのデータを作らねばならない。

* ブレッドボード用
* 回路図用
* プリント基板用

### ブレッドボード用パーツの大きさと色

{% capture text %}
### Grid & Dimensions

In order to fit into the breadboard, 
Fritzing parts must be designed with a fixed distance of 0.1 inch 
between the connector pins (legs).
Legs dimensions should be 0.03 inch wide and at least 0.03 long.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ブレッドボード用の場合、ブレッドボードに刺さるように
ブレッドボード穴のピッチでピンの位置を作図する必要がある。

ブレッドボードの穴の間隔は **0.1inch(2.54mm)** である。これは一般的な基板の穴の間隔と同じ。

そしてパーツの足の太さは最大で**0.3inch(0.762mm)**が推奨サイズである。
また、足の長さも **0.3inch以上** にしておくことが推奨されている。

また、色についても以下のように基準が設定されている

{% capture color %}
<table>
   <tbody>
      <tr>
         <th>type</th>
         <th>name</th>
         <th>color</th>
         <th>HEX</th>
         <th>RGB</th>
      </tr>
      <tr>
         <td>legs</td>
         <td>grey</td>
         <td bgcolor="8c8c8c"></td>
         <td>8c8c8c</td>
         <td>140 140 140</td>
      </tr>
      <tr>
         <td>Contacts</td>
         <td>copper/tinned</td>
         <td bgcolor="9a916c"></td>
         <td>9a916c</td>
         <td>154 145 108</td>
      </tr>
      <tr>
         <td></td>
         <td></td>
         <td bgcolor=""></td>
         <td></td>
         <td></td>
      </tr>
      <tr>
         <td>Cables</td>
         <td>blue cable</td>
         <td bgcolor="418DD9"></td>
         <td>418DD9</td>
         <td>65 141 217</td>
      </tr>
      <tr>
         <td></td>
         <td>blue cable (shadow)</td>
         <td bgcolor="1B5BB3"></td>
         <td>1B5BB3</td>
         <td>27 91 179</td>
      </tr>
      <tr>
         <td></td>
         <td>red cable</td>
         <td bgcolor="CC1414"></td>
         <td>CC1414</td>
         <td>204 20 20</td>
      </tr>
      <tr>
         <td></td>
         <td>red cable (shadow)</td>
         <td bgcolor="8C0000"></td>
         <td>8C0000</td>
         <td>140 0 0</td>
      </tr>
      <tr>
         <td></td>
         <td>black cable</td>
         <td bgcolor="404040"></td>
         <td>404040</td>
         <td>64 64 64</td>
      </tr>
      <tr>
         <td></td>
         <td>black cable (shadow)</td>
         <td bgcolor="000000"></td>
         <td>000000</td>
         <td>0 0 0</td>
      </tr>
      <tr>
         <td></td>
         <td>yellow cable</td>
         <td bgcolor="FFE24D"></td>
         <td>FFE24D</td>
         <td>255 226 77</td>
      </tr>
      <tr>
         <td></td>
         <td>yellow cable (shadow)</td>
         <td bgcolor="E6AB00"></td>
         <td>E6AB00</td>
         <td>230 171 0</td>
      </tr>
      <tr>
         <td></td>
         <td>green cable</td>
         <td bgcolor="47CC79"></td>
         <td>47CC79</td>
         <td>71 204 121</td>
      </tr>
      <tr>
         <td></td>
         <td>green cable (shadow)</td>
         <td bgcolor="00A63D"></td>
         <td>00A63D</td>
         <td>0 166 61</td>
      </tr>
      <tr>
         <td></td>
         <td>grey cable</td>
         <td bgcolor="999999"></td>
         <td>999999</td>
         <td>153 153 153</td>
      </tr>
      <tr>
         <td></td>
         <td>grey cable (shadow)</td>
         <td bgcolor="666666"></td>
         <td>666666</td>
         <td>102 102 102</td>
      </tr>
      <tr>
         <td></td>
         <td>white cable</td>
         <td bgcolor="FFFFFF"></td>
         <td>FFFFFF</td>
         <td>255 255 255</td>
      </tr>
      <tr>
         <td></td>
         <td>white cable (shadow)</td>
         <td bgcolor="999999"></td>
         <td>999999</td>
         <td>153 153 153</td>
      </tr>
      <tr>
         <td></td>
         <td>orange cable</td>
         <td bgcolor="FF7033"></td>
         <td>FF7033</td>
         <td>255 112 51</td>
      </tr>
      <tr>
         <td></td>
         <td>orange cable (shadow)</td>
         <td bgcolor="D95821"></td>
         <td>D95821</td>
         <td>217 88 33</td>
      </tr>
   </tbody>
</table>

{% endcapture %}

{% capture text %}
{{ color | strip_newlines }}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)

あまりキレイなHTMLではないのとこちらのスタイルと合わないので少しタグ構成を変更している
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



ほとんどはケーブルの色についてなのであまり必要な情報ではないが、
パーツの足やはんだの色、フットプリントの色などは従っておくべきだろう。


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/01_Breadboard.svg %}{% endcapture %}
{% capture caption %}
アートボードは作成したパーツと同じサイズにしておくと余分な余白がなくなる

穴のサイズに関しても、だいたい1.1mmぐらいがちょうどよさそう
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


### 回路図用のパーツの大きさと色

{% capture text %}
### Fonts

In the Schematics View, [Droid Sans](https://en.wikipedia.org/wiki/Droid_fonts) 
is used in different sizes according to the following hierarchy:

Part Name (4,25 points)
Pin Label (3,5 points)
Pin Group (3,5 points)
Pin Number (optional: 2,5 points)
Pin Character (3,5 points) - for example PWM

When using a different font, please converted the text into paths, 
otherwise it won't be compatible with Fritzing. 
To restate, if you are using Droid it is not necessary to convert to paths. 
In general we recommend you use the standard font and keep text as text
--we are now beginning to manipulate \<text\> elements to keep them 
from mirroring and appearing upside-down when parts are flipped and rotated, 
and Fritzing looks specifically for \<text\> elements.

[Download Fonts](http://fritzing.org/fritzings-graphic-standards/#download)

### Grid & Dimensions

The fixed distance between the connector pins in the Schematic View is 0.1 inch.
Connectors dimensions should be 0,7 points (0,0097 inch) thick and 7,2 points (0,1 inch) long.

We recommend downloading and using our templates as a reference 
while designing graphics for the Schematic View.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

各種ラベルのサイズは

パーツ名
: 4.25 pt

ピンラベル
: 3.5 pt

ピングループ
: 3.5 pt

ピンナンバー
: 2.5 pt

ピン特性
: 3.5pt

が基準となる。フォントは Droid Sans を基準にしている。

こちらもピン同士の間隔は **0.1inch(2.54mm)** が基準となっている。
ピンの線幅は **0.7pt** 、ピンの長さは **0.1inch(2.54mm)** を基準とするらしい。


{% capture text %}
### Colors

The part shape and title are in solid black (#000000 or rgb(0, 0, 0)).

Connector pins and their labels are dark grey (#555555 or rgb(85, 85, 85)).

If you group connectors, make the group label a lighter grey (#999999, rgb(153, 153, 153)).

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

つまりこういうことになる。


<table>
   <tbody>
      <tr>
         <th>name</th>
         <th>color</th>
         <th>HEX</th>
         <th>RGB</th>
      </tr>
      <tr>
         <td>partshape</td>
         <td bgcolor="000000"></td>
         <td>000000</td>
         <td>0 0 0</td>
      </tr>
      <tr>
         <td>title</td>
         <td bgcolor="000000"></td>
         <td>000000</td>
         <td>0 0 0</td>
      </tr>
      <tr>
         <td>connector pin</td>
         <td bgcolor="555555"></td>
         <td>555555</td>
         <td>85 85 85</td>
      </tr>
      <tr>
         <td>pin label</td>
         <td bgcolor="555555"></td>
         <td>555555</td>
         <td>85 85 85</td>
      </tr>
      <tr>
         <td>group label</td>
         <td bgcolor="999999"></td>
         <td>999999</td>
         <td>153 153 153</td>
      </tr>

   </tbody>
</table>


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/02_Schemantic.svg %}{% endcapture %}
{% capture caption %}
ピンの配置は 左上始まりの 0.1inch(2.54mm) のグリッドに沿うようにするときれいに整う

ピンの外側のアンカー基準で 0.7pt の円を作成しこれを接続口にすると、
画像端から配線が伸びてきれいになる

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## プリント基板用パーツの大きさと色

{% capture text %}
### Fonts

The PCB footprint should not have any text, 
as it is dynamically created when working with Fritzing. 
If you'd still like to add some text, best to use the font "OCR A".


### Dimensions

A Part's dimensions and distance between connector pins in the PCB View 
should be exactly as in the real world. 
This is extremely important for a successful pcb production. 
We highly recommend checking a part's dimensions in its datasheet.

Here are a couple of things to keep in mind:

1. The stroke of a footprint should be at least 20mils (~0.5mm) thick, 
otherwise the connection might be ruined while drilling and soldering will be much harder.
2. The hole itself should be at least 40mils (~1mm) thick, 
so you would actually need to create a circle which is at least 60mils (~1.5mm) wide 
in diameter, because half of the stroke is drawn inside the circle.
3. If your part fits on the breadboard, 
the distance between the footprints should be 100mils (~2.5mm) long.

We also recommend downloading and using our templates as a 
reference while designing graphics for the PCB View.
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


実世界のサイズに合うようなパターンを彫らなければならないので、しっかりとサイズを確認すること、
と強く推奨されている。

実際のところ、

1. はんだ付けする場所(フットプリント)は **0.5mm** 以上ないときびしい
2. 穴を開けるパターンの場合、**直径1.5mm**ほど、**1.2pt**ほどの大きさ、線幅にすること
    * 丸いパスの大きさで穴を開けるので、線幅の半分は削られてしまうから少し大きめにする
3. ブレッドボードに合うサイズの場合、パターン同士の間隔は**0.1inch(2.54mm)**ほど必要

ということらしい。



{% capture text %}
### Colors

The PCB View uses the following colors:

{% capture color%}
<table>
   <tbody>
      <tr>
         <th>type</th>
         <th>name</th>
         <th>color</th>
         <th>HEX</th>
         <th>RGB</th>
      </tr>
      <tr>
         <td>Connector</td>
         <td>copper</td>
         <td bgcolor="f7bd13"></td>
         <td>F7BD13</td>
         <td>247 189 19</td>
      </tr>
      <tr>
         <td>Silkscreen</td>
         <td>black</td>
         <td bgcolor="000000"></td>
         <td>000000</td>
         <td>0 0 0</td>
      </tr>
   </tbody>
</table>
{% endcapture %}

{{ color | strip_newlines }}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Graphic Standards](http://fritzing.org/fritzings-graphic-standards/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


こういうことになるらしい。


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/03_PCB.svg %}{% endcapture %}
{% capture caption %}
それぞれ推奨する最小サイズ、ブレッドボードの場合の間隔など
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

また、プリント基板用のパーツデータにはレイヤー構造にも制約が存在する。

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/04_Restriction.png %}{% endcapture %}
{% capture caption %}
何やらエラーが出る
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


これはフットプリントのデータが

* copper0
    * copper1
        * footprint1
        * footprint2
        * ...

というグループ/レイヤー構造の中に入っていない場合、エラーとなる。


{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/05_Copper_layer.png %}{% endcapture %}
{% capture caption %}
レイヤー構造をこのようにしなければならない
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## データのテンプレート

仕様の確認はできた。
では実際にどうやって作ればいいのか、というテンプレートファイルが存在している。


{% capture text %}

You can download and use these helpful templates 
to design your custom parts according to Fritzing's Graphic Standards.

The following .zip file includes:

1. Droid Sans font
2. OCRA font
3. Breadboard View graphics template
4. Schematic View graphics template
5. PCB View graphics template


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Fritzing - Download fonts and templates](http://fritzing.org/fritzings-graphic-standards/download-fonts-and-templates)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





## 最終データ

最終的に作ってみたデータがこちら

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/06_FritzingData.png %}{% endcapture %}
{% capture caption %}
それぞれをSVG出力すれば読み込ませる事ができる
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


アートボードのサイズは基本的にパーツと同じサイズにしておくこと。

Illustrator CC出力時の設定は以下

{% capture url %}{{ site.github.url }}{% link _docs/Arduino/images/fritzing-part/07_Settings.png %}{% endcapture %}
{% capture caption %}

* SVG1.2 Tiny
* レスポンシブのチェックを切る

{% capture text %}

Thanks to [@chidea](http://forum.fritzing.org/u/chidea)’s lead, 
I’ve found a definitive solution.
Adobe Illustrator CC has an option (enabled by default) to save SVG as 
"responsive" (that is, scalable in web pages).
Just click on the “More Options” button on the Save pane, 
while saving as a Tiny 1.2 SVG, and deselect “"esponsive".
Then the file will load correctly with Fritzing without the need to edit it.

Thanks to
<https://github.com/filamentgroup/grunticon/issues/12713>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by [zontar](http://forum.fritzing.org/u/zontar)

from [Error loading SVG files to Parts Editor - parts help - fritzing forum](http://forum.fritzing.org/t/error-loading-svg-files-to-parts-editor/963/14)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



