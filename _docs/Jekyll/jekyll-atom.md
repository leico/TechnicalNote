---
layout : post
title  : Collectiosの変更に追従するAtomフィードを設置する
date : 2017/06/10
lastchange : 2017-06-10 17:10:58.
tags   :
  - jekyll
  - github
  - pages
  - atom
  - feed
---

## **\_posts** の変更は既存のもので追従するけれど

ページ右下にAtomリンクあります。

Wiki的に使っているこのサイトはページを大まかなカテゴリ毎に Collection を作って更新している。

最近は直下に **\_** から始まるディレクトリが多くなりすぎてカオスになっていたので **\_docs** に集約させた。

で、 Collection 内のページはそのままでは追従しない。Collection の変更も追従するAtomフィードを作成する。

参考:

* [Atomフィード - Jekyll Tips](http://jekylltips-ja.github.io/tutorials/atom-feed/)
* [RSS1.0,RSS2.0,ATOM のフォーマット・仕様・構造 - Amarronの日記](http://amarron.hatenablog.com/entry/2014/03/14/200556)
* [1つのグラフに見るRSSの死 \| SEO Japan – アイオイクスによる海外最新SEO情報ブログ](http://www.seojapan.com/blog/death-of-rss)
* [RSSは死んでいく技術？  –  Rriver](http://parashuto.com/rriver/marketing/is-rss-dying)
* [Atom 1.0 のフォーマット｜RSS｜Web関連特集｜PHP & JavaScript Room](http://phpjavascriptroom.com/?t=topic&p=atom_format)
* [  RSSに対応、RSSの書き方 : SITE-ICHIJO. NET/BLOG 跡地](http://si.jpn.org/archives/date/2006/1028-230856.php)
* [Introduction to Atom](https://validator.w3.org/feed/docs/atom.html)
* [Data Files \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/datafiles/)
* [Concat arrays in Jekyll(liquid)](https://gist.github.com/BryanSchuetz/52012affd9318ba59e19a74639a8c16a)
* [Jekyllのテーマを自作する 基本編 – 第5回 – titleタグとmeta descriptionを作成する \| e-JOINT.jp](http://e-joint.jp/363/)
* [liquid - How can I split a string by newline in Shopify? - Stack Overflow](https://stackoverflow.com/questions/27694610/how-can-i-split-a-string-by-newline-in-shopify)


## [Jekyll Tips](http://jekylltips-ja.github.io/) のひな形を利用する

そもそもAtomフィードを書いた経験がないので色々調べながら進める。

......のはいつものことだけれど、Jekyll-tipsにひな形が掲載されていたのでこれを元に改造する。



> ## JekyllでのAtomフィード
> 
> Atomフィードによって、読者はあなたのブログの最新の記事を購読することができます。AtomフィードをJekyllサイトに追加するのは簡単です。
> 
> 以下の内容で、ウェブサイトルートに`atom.xml`を作ります：
> 
> {% raw %}
> ```html
> ---
> layout: null
> ---
> 
> <?xml version="1.0" encoding="utf-8"?>
> <feed xmlns="http://www.w3.org/2005/Atom">
>   <title>{{ site.name }}</title>
>   <link href="{{ site.url }}/atom.xml" rel="self" />
>   <link href="{{ site.url }}/"/>
>   <updated>{{ site.time | date_to_xmlschema }}</updated>
>   <id>{{ site.url }}</id>
>   <author>
>     <name>{{ site.author.name }}</name>
>     <email>{{ site.author.email }}</email>
>   </author>
>   {% for post in site.posts %}
>     <entry>
>       <title>{{ post.title }}</title>
>       <link href="{{ site.url }}{{ post.url }}" />
>       <updated>{{ post.date | date_to_xmlschema }}</updated>
>       <id>{{ site.url }}{{ post.id }}</id>
>       <content type="html">{{ post.content | xml_escape }}</content>
>     </entry>
>   {% endfor %}
> </feed>
> ```
> {% endraw %} 
> このコードは、すべてのブログ記事に繰り返し処理を行い、記事全てをXML形式で出力しています。
> 
> ---
> 
> * [Atomフィード - Jekyll Tips](http://jekylltips-ja.github.io/tutorials/atom-feed/)

これを見ただけでは全然わからないので調べる。




## Atomフィードについて

> ### RSS1.0
> 
> * シンプルな記述がウリ。テキスト配信向き？
> * RDFシリーズを元にして制作されている。
> * はてなRSSは1.0を使用。FC2も1.0を使用。
> 
> ### RSS2.0
> 
> * 配信する文章の色を変えたり、リンクを仕込めるなど、グラフィック面でいろいろ出来る。コンテンツ配信向き？
> * RSS1.0の後継シリーズではない。(0.9x系の流れを汲む規格)
> * XMLシリーズを元にして制作されている。
> 
> ### ATOM
> 
> * RSS2.0からいろいろ削って、よりシンプルに。よりスマートに。
> * GoogleはGmail（ジーメール） にて、メールの内容を Atom フィードで提供するサービスを行っている。
> 
> ---
> 
> * [RSS1.0,RSS2.0,ATOM のフォーマット・仕様・構造 - Amarronの日記](http://amarron.hatenablog.com/entry/2014/03/14/200556)

## 利用状況

* [1つのグラフに見るRSSの死 \| SEO Japan – アイオイクスによる海外最新SEO情報ブログ](http://www.seojapan.com/blog/death-of-rss)
* [RSSは死んでいく技術？  –  Rriver](http://parashuto.com/rriver/marketing/is-rss-dying)

などなど、そもそもフィードはオワコンみたいな流れはある。
僕も使ったことがない。が、信頼できるソースの更新状況が手に入るというのはなかなか便利なのだろう。

作っておいて損はないか。Github Pages/Jekyll/Max等ニッチな世界のニッチな検証をまとめている場所だし。
けれどRSS/Atom両対応まではしなくてもよいかな。




## Atomフィードの構造

> ## Atom 1.0 の基本構造
> 
> ```xml
> <?xml version='1.0' encoding='UTF-8'?>
> <feed xmlns='http://www.w3.org/2005/Atom' xml:lang='ja'>
> 	<id>tag:phpjavascriptroom.comfeed/</id>
> 	<title>PHP & JavaScript Room：更新情報</title>
> 	<updated>2008-06-11T15:30:59Z</updated>
> 	<link rel='alternate' type='text/html' href='http://phpjavascriptroom.com/feed/' />
> 	<link rel='self' type='application/atom+xml' href='http://phpjavascriptroom.com/feed/atom10.xml' />
> 	<entry>
> 		<id>http://phpjavascriptroom.com/post3.html#20080611</id>
> 		<title>記事タイトル3</title>
> 		<link rel='alternate' type='text/html' href='http://phpjavascriptroom.com/post3.html' />
> 		<updated>2008-06-11T15:30:59Z</updated>
> 		<summary>記事の内容です。</summary>
> 	</entry>
> 	<entry>
> 		<id>http://phpjavascriptroom.com/post2.html#200810153059</id>
> 		<title>記事タイトル2</title>
> 		<link rel='alternate' type='text/html' href='http://phpjavascriptroom.com/post2.html' />
> 		<updated>2008-06-10T15:30:59Z</updated>
> 		<summary>記事の内容です。</summary>
> 	</entry>
> 	<entry>
> 		<id>http://phpjavascriptroom.com/post1.html#20080609205030</id>
> 		<title>記事タイトル1</title>
> 		<link rel='alternate' type='text/html' href='http://phpjavascriptroom.com/post1.html' />
> 		<updated>2008-06-09T20:50:30Z</updated>
> 		<summary>記事の内容です。</summary>
> 	</entry>
> </feed>
> ```
>
> ---
>
> * [Atom 1.0 のフォーマット｜RSS｜Web関連特集｜PHP & JavaScript Room](http://phpjavascriptroom.com/?t=topic&p=atom_format)

> 作るファイルはひとつだけ、RSSファイルです。それにリンクを貼るだけ。
> 以下ソース。テクストエディタなどで編集してください。
> 注： （説明） ←カッコごと書き換えてください。
> 
> ```xml
> <?xml version=”1.0″ encoding=”utf-8″ ?>
>   <rss version=”2.0″>
>     <channel>
>       <title>（サイト名）</title>
>       <link>（サイトのURL）</link>
>       <description>（サイトの説明、ない場合は空欄）</description>
>       <lastBuildDate>Sat, 28 Oct 2006 00:00:00 +0900</lastBuildDate>
>       （↑最終更新日時。曜日, 日 月 年 時:分:秒 +0900）
> 
>       <item>
>         <title>（各情報のタイトル）</title>
>         <description><![CDATA[（各情報の説明）]]></description>
>         <link>（各情報のURL）</link>
>         <category>（カテゴリをつけたい場合は入力。「お知らせ」「更新情報」など）</category>
>         <pubDate>Sat, 28 Oct 2006 00:00:00 +0900</pubDate>
>         （↑各情報の日時。書式は上に同じ）
>       </item>
> 
>       （複数情報を入力したい場合は<item>～</item>を連記。その場合は上から新しい順になるように）
>       <item>
>       ・
>       ・
>       ・
>       </item>
> 
>       </channel>
>     </rss>
> ```
> 
> ---
> 
> * [  RSSに対応、RSSの書き方 : SITE-ICHIJO. NET/BLOG 跡地](http://si.jpn.org/archives/date/2006/1028-230856.php)



こうやってみる限り、`<entry></entry>`を記事の数だけ用意する。`<entry></entry>`の中身は

* タイトル
* エントリーへのリンク
* 概要
* 投稿日時

という感じ。ついで、先頭にホームページ全体の情報を

* ID
* タイトル
* ページリンク
* 著者
* 最終更新日

とかを記述するらしい。詳しくはW3Cを見ろ...と。

* [Introduction to Atom](https://validator.w3.org/feed/docs/atom.html)

ひな形をいじってみる。

## Atomフィードの作成

最終的にこうなった。

{% raw %}
```xml
---
layout: null
---

<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">

  <title>{{ site.name }}</title>
  <link href="{{ site.url }}/atom.xml" rel="self" />
  <link href="{{ site.url }}/"/>
  <updated>{{ site.time | date_to_xmlschema }}</updated>
  <id>{{ site.url }}</id>
  <author>
    <name>{{ site.data.summary.author.name }}</name>
  </author>

  {% assign docs = "" | split: "" %}
  {% for collection in site.collections %}
    {% for doc in collection.docs %}
    
    {% if doc.slug == 'index' %} {% continue %} {% endif %}
    {% assign docs = docs | push: doc %}

    {% endfor %}
  {% endfor %}


  {% assign docs = docs | sort: 'lastchange' | reverse %}
  {% for doc in docs %}
    <entry>
      <title>{{ doc.title }}</title>
      <link href="{{ site.github.url }}{{ doc.url }}" />
      <updated>
        {{ doc.lastchange | date_to_xmlschema }}
      </updated>
      <id>{{ site.github.url }}{{ doc.id }}</id>
      <summary>{{ doc.content | strip_html | newline_to_br | split:'<br />' | first }}</summary>
    </entry>
  {% endfor %}
</feed>
```

### ページ情報

```xml
  <title>{{ site.name }}</title>
  <link href="{{ site.url }}/atom.xml" rel="self" />
  <link href="{{ site.url }}/"/>
  <updated>{{ site.time | date_to_xmlschema }}</updated>
  <id>{{ site.url }}</id>
  <author>
    <name>{{ site.data.summary.author.name }}</name>
  </author>
```

> ## The Data Folder
> 
> As explained on the [directory structure](https://jekyllrb.com/docs/structure/) page, 
> the `_data` folder is where you can store additional data for Jekyll to use when generating your site. 
> These files must be YAML, JSON, or CSV files (using either the `.yml`, `.yaml`, `.json` or `.csv` extension),
> and they will be accessible via `site.data`.
> 
> ## Example: List of members
> 
> Here is a basic example of using Data Files 
> to avoid copy-pasting large chunks of code in your Jekyll templates:
> 
> In `_data/members.yml`:
> 
> ```yaml
> - name: Eric Mill
>   github: konklone
> 
> - name: Parker Moore
>   github: parkr
> 
> - name: Liu Fengyun
>   github: liufengyun
> ```
> 
> Or `_data/members.csv`:
> 
> ```
> name,github
> Eric Mill,konklone
> Parker Moore,parkr
> Liu Fengyun,liufengyun
> ```
> 
> This data can be accessed via `site.data.members` 
> (notice that the filename determines the variable name).
> 
> ---
> 
> * [Data Files \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/datafiles/)

ページ情報はほとんど変更していない。1点、オーナー情報等は Jekyll Documentation に 
`_data` にまとめる example が掲載されていたのでこれに倣っている。

### Collectionsページの走査

```liquid
  {% assign docs = "" | split: "" %}
  {% for collection in site.collections %}
    {% for doc in collection.docs %}
    
    {% if doc.slug == 'index' %} {% continue %} {% endif %}
    {% assign docs = docs | push: doc %}

    {% endfor %}
  {% endfor %}
```

> ```liquid
> {% assign all_hosts = "" | split: "" %}
> {% for host in site.data.shared_hosts %}
>   {% assign all_hosts = all_hosts | push: host %}
> {% endfor %}
> {% for host in site.data.paas_hosts %}
>   {% assign all_hosts = all_hosts | push: host %}
> {% endfor %}
> ```
> 
> ---
> 
> * [Concat arrays in Jekyll(liquid)](https://gist.github.com/BryanSchuetz/52012affd9318ba59e19a74639a8c16a)

上記の方法を利用した。
空文字を空文字で分割して配列作る方法は賢い。

後は全ての Collection -> docs を docs にまとめている

### `<entry></entry>`の作成

```xml
  {% assign docs = docs | sort: 'lastchange' | reverse %}
  {% for doc in docs %}
    <entry>
      <title>{{ doc.title }}</title>
      <link href="{{ site.github.url }}{{ doc.url }}" />
      <updated>
        {{ doc.lastchange | date_to_xmlschema }}
      </updated>
      <id>{{ site.github.url }}{{ doc.id }}</id>
      <summary>{{ doc.content | strip_html | newline_to_br | split:'<br />' | first }}</summary>
    </entry>
  {% endfor %}
```

```liquid
 {% assign docs = docs | sort: 'lastchange' | reverse %}
```

`docs` を更新日時の新しい順にソートし、新しい方から`<entry></entry>`を記述している。


> meta descriptionはこんな感じに表示できるようにします｡
> 
> * トップページではサイトの説明文を表示する
> * それ以外ページでは､ページの概要を表示する（160文字）
> 
> If文を使って以下の用に書き換えます｡
> 
> ```xml
> <meta name="description" content="{% if page.excerpt %}{{ page.excerpt | strip_html | strip_newlines | truncate: 160 }}{% else %}{{ site.description }}{% endif %}">
> ```
>
> ---
>
> * [Jekyllのテーマを自作する 基本編 – 第5回 – titleタグとmeta descriptionを作成する \| e-JOINT.jp](http://e-joint.jp/363/)


> Try this:
> 
> ```liquid
> {% assign paragraphs = settings.intro | newline_to_br | split: '<br />' %}
> {% for paragraph in paragraphs %}<p>{{ paragraph }}</p>{% endfor %}
> ```
>
> ---
>
> answered Dec 29 '14 at 21:23
> 
> [Josh Brown](https://stackoverflow.com/users/2423824/josh-brown)
>
> ---
>
> * [liquid - How can I split a string by newline in Shopify? - Stack Overflow](https://stackoverflow.com/questions/27694610/how-can-i-split-a-string-by-newline-in-shopify)

などを参考に、概要は最初の1文だけを表示するようにした

```liquid
{{ doc.content | strip_html | newline_to_br | split:'<br />' | first }}
```

## `_config.yml` に *jekyll-feed* 追加

最後に、普段から勝手に **feed.xml** を出力している *jekyll-feed* の設定を上書きする。

`gems` に `jekyll-feed` 追加

```yml
gems:
  - jekyll-gist
  - jekyll-mentions
  - jekyll-feed
```

*jekyll-feed* 用の設定を追加


```yml
# ---- jekyll-feed -----
feed : 
  path : atom.xml
```

Atomフィードへのリンクは右下

{% endraw %}
