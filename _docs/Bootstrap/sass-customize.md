---
layout : post
title  : Bootstrap sassをカスタマイズする
date   : 2017/04/01
lastchange : 2017-04-02 00:03:42.
tags   :
  - Bootstrap
  - sass
  - jekyll
  - github pages
---

## デフォルトだとやっぱりみにくい

というか本文の文字小さすぎる。一方で引用の文字大きすぎる。

ということで *style.sass*をカスタマイズした。

```sass
$font-size-base : 16px

$blockquote-font-size: $font-size-base


//import bootstrap
@import "bootstrap/_bootstrap"
@import "_syntax"

pre
  font-size : $font-size-small

.MathJax_Display
  font-size : $font-size-large

h1, .h1, h2, .h2, h3, .h3
  margin-top : $font-size-h1
```

参考

* [Bootstrap実践編 Sass,CSSを追加してオリジナルデザインにする（４） - マンガでなれる？WEBデザイナー講座](https://creativeweb.jp/manga/bootstrap-2/page4)
* [Sass: Sass Basics](http://sass-lang.com/guide)
* [変数宣言の!defaultは「既に定義されている場合は上書きしない」という意味 - Qiita](http://qiita.com/yuku_t/items/0bebe541432cdfad4e14)

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
