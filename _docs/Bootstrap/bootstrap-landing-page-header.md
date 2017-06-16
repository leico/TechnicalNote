---
layout : post
title  : bootstrap:ランディングページ(トップページ)のヘッダについて考える
date : 2017/02/12
lastchange : 2017-06-15 16:29:49.
tags   :
  - bootstrap
  - jumbotron
  - landing page
  - toppage
  - header
---

## Jekyllのヘッダ部分どうしよう

ってなり、bootstrapやらHTML5やらの書き方を色々調べた。

参考:

* [ランディングページとは何か、これで分かる！ \| Ｗｅｂ集客の開花塾](https://web60.co.jp/landing-page.html)
* [Bootstrapを使ってWEBサイトを作ってみようぜ 第6回 「ヘッダー部分を作る」](http://9-bb.com/?p=8782)
* [Bootstrapを使ってみる。ヘッダー編（Jumbotron）\| みずかず](http://mizukazu.minibird.jp/bootstrap-jumbotron/)
* [ジャンボトロン ≪ コンポーネント ≪ Bootstrap3日本語リファレンス](http://bootstrap3.cyberlab.info/components/jumbotron.html)
* [Jumbotron - Bootstrap](https://v4-alpha.getbootstrap.com/components/jumbotron/)
* [HTML5 でのサブタイトルのマークアップについて - 破棄されたブログ](http://hateda.hatenadiary.jp/entry/2013/05/09/000124)
* [Components - Bootstrap#Page header](http://getbootstrap.com/components/#page-header)

## 結果としてこうなった

```html
<div class="jumbotron">
  <header>
    <h1>Page Title</h1>
    <p>subtitle</p>
  </header>
  <p>description</p>
</div>
```

こうなった理由は以下。

## 大きなヘッダをきれいに簡単に作りたい

ブログのヘッダや最近のWebページのトップみたいに、大きいヘッダを簡単に作ることはできないかと調べた。

{% assign text='
ランディングページ（landing page）とは、直訳すれば着地ページで、ユーザーが最初に訪問するWebページになります。
' | markdownify %}
{% assign source='
[ランディングページとは何か、これで分かる！ \| Ｗｅｂ集客の開花塾](https://web60.co.jp/landing-page.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

最近はトップページのことをランディングページと言うらしい。

{% assign text='
bootstrap3からはjumbotronっていうのが実装されて、以前はhero-unitと呼ばれてたものが置き換わったようです。
このjumbotronっていうのを使うと、簡単にヘッダー部分が出来上がります
' | markdownify %}
{% assign source='
[Bootstrapを使ってWEBサイトを作ってみようぜ 第6回 「ヘッダー部分を作る」](http://9-bb.com/?p=8782)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

__jumbotron__ というものを使えば大きなスペースを持つヘッダが簡単にできるようだ。

{% assign text='
ソースコード

```html
<div class="jumbotron">
	<h1>ジャンボトロン</h1>
	<p>WEBサイトにおける主要コンテンツを紹介するのに適したスタイル。</p>
	<p><a class="btn btn-primary btn-lg" href="./jumbotron.html" role="button">もっと詳しく</a></p>
</div>
```

' | markdownify %}
{% assign source='
[ジャンボトロン ≪ コンポーネント ≪ Bootstrap3日本語リファレンス](http://bootstrap3.cyberlab.info/components/jumbotron.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

なるほど。

サブタイトルはどうやって書こうか。

{% capture text %}
##### タイトルとタグラインを header 要素でグループ化した例

タイトルを h2 要素、タグラインを p 要素でマークアップして、header 要素でグループ化している。
(引用元 [Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%A9%E3%83%96%E3%83%A9%E3%82%A4%E3%83%96!))

```html
<header>
   <h2>ラブライブ! School idol project</h2>
   <p>雑誌『電撃G's magazine』（アスキー・メディアワークス）・ランティス・サンライズの合同ユーザー参加企画、および関連作品の名義。</p>
</header>
```

```sh
section
| + header title
| | + h1 main title
| | + p subtitle
| + [contents]
```
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[HTML5 でのサブタイトルのマークアップについて - 破棄されたブログ](http://hateda.hatenadiary.jp/entry/2013/05/09/000124)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



他にも例はいくつか載っているがこれがいい気がする。

{% assign text='
# Page header

A simple shell for an h1 to appropriately space out and segment sections of content on a page.
It can utilize the h1's default small element, as well as most other components (with additional styles).

<div class="page-header">
  <h1>Example page header <small>Subtext for header</small></h1>
</div>

```html
<div class="page-header">
  <h1>Example page header <small>Subtext for header</small></h1>
</div>
```
' | markdownify %}
{% assign source='
[Components - Bootstrap#Page header](http://getbootstrap.com/components/#page-header)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

bootstrapはこうしている。

