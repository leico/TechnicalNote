---
layout : post
title  : Bootstrap sassをカスタマイズする
date   : 2017/04/01
lastchange : 2017-06-10 18:07:12.
tags   :
  - Bootstrap
  - sass
  - jekyll
  - github pages
---

## デフォルトだとやっぱりみにくい

ということで *style.sass* をカスタマイズした。

```sass

// Colors
//
// Grayscale and brand colors for use across Bootstrap.
// Start with assigning color names to specific hex values.

$gray-dark     : #333333 //K 80%
$gray          : #666666 //K 60%
$gray-light    : #999999 //K 40%
$gray-lighter  : #cccccc //K 20%
$gray-lightest : #f3f3f3 //K 5%

$white  : #fff
$black  : #000
$blue   : #2996cc //base H200 S80 V80
$teal   : #29CCB1
$green  : #29CC5F
$yellow : #CCB129
$orange : #CC5F29
$red    : #CC2944
$pink   : #CC2996
$purple : #B129CC

// Body
//
// Settings for the `<body>` element.
$body-bg    : $gray-lightest
$body-color : $gray-dark

$inverse-bg    : $gray-lightest
$inverse-color : $gray-dark

// Links
//
// Style anchor elements.
$link-hover-color: $teal


// Fonts
//
// Font, line-height, and color for body text, headings, and more.
$line-height-base     : 1.55
$headings-line-height : $line-height-base
$blockquote-font-size : 1rem
$letter-spacing       : 0.01rem

// Jumbotron
$jumbotron-bg : $gray-lightest

// Cards
$card-bg : $gray-lightest

// Code
$code-color : $yellow
$code-bg    : $gray-dark


//import bootstrap
@import "bootstrap/bootstrap"
@import "_syntax"

body
  letter-spacing : $letter-spacing


pre
  font-size : $font-size-sm

.MathJax_Display
  font-size : $font-size-lg

.card
  @extend .my-1

.card h1
  font-size : $font-size-h3
.card h2
  font-size : $font-size-h4
.card h3
  font-size : $font-size-h5
.card h4
  font-size : $font-size-h6
.card h5, .card h6
  font-size : 1rem

h1:not(.card-header), 
h2:not(.card-header),
h3:not(.card-header),
.h1, .h2, .h3
  @extend .my-5

table
  @extend .table

blockquote
  @extend .blockquote

p
  @extend .text-justify

.card-block > p
  @extend .card-text

pre
  @extend .p-3
  @extend .rounded

div.card-columns.category-index
  @include media-breakpoint-only(md)
    column-count : 1
  @include media-breakpoint-only(lg)
    column-count : 3
```

参考

* [Bootstrap実践編 Sass,CSSを追加してオリジナルデザインにする（４） - マンガでなれる？WEBデザイナー講座](https://creativeweb.jp/manga/bootstrap-2/page4)
* [Sass: Sass Basics](http://sass-lang.com/guide)
* [変数宣言の!defaultは「既に定義されている場合は上書きしない」という意味 - Qiita](http://qiita.com/yuku_t/items/0bebe541432cdfad4e14)
* [#333333 Color Hex](http://www.color-hex.com/color/333333)
* [#2996ccの色見本とカラーコード](http://www.colordic.org/colorsample/2996cc.html)
* [品のある青色（Webカラー#2996CC）を使ったカラーパレット \| 配色の悩みを解決！配色の見本帳](http://ironodata.info/rgb.php?color=2996CC)
## 変数の値を変更する

> ```sass
> $font-stack:    Helvetica, sans-serif
> $primary-color: #333
> 
> body
>   font: 100% $font-stack
>   color: $primary-color
> ```
> 
> ---
> 
> * [Sass: Sass Basics](http://sass-lang.com/guide)

こうやって変数を使えるらしい。`;`が最後にいらないのがちょっと気持ち悪い。

## bootstrap sass を眺める

> style.scssから_variables.scssのNavbarを探して下記の赤文字の部分を変更します。
> 
> ---
> * [Bootstrap実践編 Sass,CSSを追加してオリジナルデザインにする（４） - マンガでなれる？WEBデザイナー講座](https://creativeweb.jp/manga/bootstrap-2/page4)

*\_variables.scss* の中に色々な変数が宣言されているようだ。
ここから必要なものだけを上書きする。

## bootstrap sass を上書きする

> Sassで変数を定義するときに `!default` がついていることがありますが、
> これは「あとから上書きされます」という意味ではなく「既に定義されている場合は上書きしません」という意味らしい。
> 
> ---
> 
> * [変数宣言の!defaultは「既に定義されている場合は上書きしない」という意味 - Qiita](http://qiita.com/yuku_t/items/0bebe541432cdfad4e14)

なので、文字サイズを先行して宣言しておけば上書きされる。

文字サイズの変数を *bootstrap/bootstrap/_variables.scss* から探すと`$base-font-size` というのがみつかったのでこれを変更する。
これで`$base-font-size` を基準に全てのフォントサイズがいい感じにアレンジされる。

今度はヘッダの上のマージンが少ないのが気になったり、MathJaxのフォントサイズが気になったり。

Bootstrapで宣言されている変数を利用したいので、`@import`の後にこれらの設定を記述する。
変数が宣言される前に書いてしまうと当然ながらエラーが返ってくる。

sassでの記法はこんな感じらしい

> ```sass
> nav
>   ul
>     margin: 0
>     padding: 0
>     list-style: none
> 
>   li
>     display: inline-block
> 
>   a
>     display: block
>     padding: 6px 12px
>     text-decoration: none
> ```
> 
> ---
> 
> * [Sass: Sass Basics](http://sass-lang.com/guide)

これが

> ```css
> nav ul {
>   margin: 0;
>   padding: 0;
>   list-style: none;
> }
> 
> nav li {
>   display: inline-block;
> }
> 
> nav a {
>   display: block;
>   padding: 6px 12px;
>   text-decoration: none;
> }
> ```
> 
> ---
> 
> * [Sass: Sass Basics](http://sass-lang.com/guide)

こうなる。

## 色指定

```sass
// Colors
//
// Grayscale and brand colors for use across Bootstrap.
// Start with assigning color names to specific hex values.

$gray-dark     : #333333 //K 80%
$gray          : #666666 //K 60%
$gray-light    : #999999 //K 40%
$gray-lighter  : #cccccc //K 20%
$gray-lightest : #f3f3f3 //K 5%

$white  : #fff
$black  : #000
$blue   : #2996cc //base H200 S80 V80
$teal   : #29CCB1
$green  : #29CC5F
$yellow : #CCB129
$orange : #CC5F29
$red    : #CC2944
$pink   : #CC2996
$purple : #B129CC
```

CMYK の \\( K = 80 \\% \\) くらいを基準にしながらグレイスケールを作成した。

カラーについては HSV の \\( S = 80 \\% \\),\\(V = 80 \\% \\) くらいを基準に Hue を回した。

参考:

* [#333333 Color Hex](http://www.color-hex.com/color/333333)
* [#2996ccの色見本とカラーコード](http://www.colordic.org/colorsample/2996cc.html)
* [品のある青色（Webカラー#2996CC）を使ったカラーパレット \| 配色の悩みを解決！配色の見本帳](http://ironodata.info/rgb.php?color=2996CC)

## cssデータの継承

Bootstrap は class 指定をしないとスタイルが適用されない仕様になっているので、
markdown から吐き出された html には基本、装飾がなされない。

class 指定されていない要素もスタイルが適用されるように、class の設定を各々のタグに継承させる。


> ## Extend/Inheritance
> 
> This is one of the most useful features of Sass. 
> Using `@extend` lets you share a set of CSS properties from one selector to another. 
> It helps keep your Sass very DRY. 
> In our example we're going to create a simple series of messaging for errors, warnings and successes.
> 
> ### Sass Syntax
> 
> ```sass
> .message
>   border: 1px solid #ccc
>   padding: 10px
>   color: #333
> 
> 
> .success
>   @extend .message
>   border-color: green
> 
> 
> .error
>   @extend .message
>   border-color: red
> 
> 
> .warning
>   @extend .message
>   border-color: yellow
> ```
> 
> 
> What the above code does is allow you to take the CSS properties in 
> `.message` and apply them to `.success`, `.error`, & `.warning`. 
> The magic happens with the generated CSS, 
> and this helps you avoid having to write multiple class names on HTML elements. 
> This is what it looks like:
> 
> 
> ```css
> .message, .success, .error, .warning {
>   border: 1px solid #cccccc;
>   padding: 10px;
>   color: #333;
> }
> 
> .success {
>   border-color: green;
> }
> 
> .error {
>   border-color: red;
> }
> 
> .warning {
>   border-color: yellow;
> }
> ```
> 
> ---
>
> * [Sass: Sass Basics](http://sass-lang.com/guide)
