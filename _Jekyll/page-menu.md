---
layout : post
title  : ページメニューの作り方
date   : 2017/03/04
lastchange : 2017-03-05 03:19:31.
tags   :
  - jekyll
  - liquid
  - github
  - pages
---

## ページメニューの作り方

ブログというより、どちらかといえばこのページはWiki的に使いたいので、Wikiのサイドバーのようなメニューの作り方を調べた。

参考

* [jekyll/docs.yml at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_data/docs.yml)
* [Data Files - Jekyll](https://jekyllrb.com/docs/datafiles/)
* [jekyll/docs\_contents.html at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_includes/docs_contents.html)
* [Using Jekyll \_includes as custom liquid functions](http://hamishwillee.github.io/2014/11/13/jekyll-includes-are-functions/)
* [jekyll/docs\_ul.html at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_includes/docs_ul.html)
* [Collections - Jekyll](https://jekyllrb.com/docs/collections/)
* [Templates - Jekyll](https://jekyllrb.com/docs/templates/)
* [jekyll/docs/\_docs at jekyll/jekyll](https://github.com/jekyll/jekyll/tree/master/docs/_docs)
* [Liquid for Designers - Shopify](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)
* [Jekyll & Liquid Cheat Sheet](https://gist.github.com/smutnyleszek/9803727)

## 本家Jekyllのメニューバー

まず本家[Jekyll Docs](https://jekyllrb.com/docs/home/)のメニューバーはどうやっているのか調べた。

### 前知識 **\_data** ディレクトリ

> ## The Data Folder
> 
> As explained on the [directory structure](https://jekyllrb.com/docs/structure/) page, 
> the `_data` folder is where you can store additional data for Jekyll to use when generating your site.
> These files must be YAML, JSON, or CSV files (using either the `.yml`, `.yaml`, `.json` or `.csv` extension),
> and they will be accessible via `site.data`.
> 
> ---
> 
> * [Data Files - Jekyll](https://jekyllrb.com/docs/datafiles/)

まず **\_data** ディレクトリについて知らないといけないようだ。

**\_data** ディレクトリに入っているYAMLファイル等の情報に、`site.data.*`でアクセスができる。
`*`の部分にはまずファイル名が入る。ファイル名の後はデータ構造による。
引用したソースの **Getting Started** を手に入れたい場合は、

{% raw %}
```liquid
{{ site.data.docs[0].title }}
```
{% endraw %}

となる。

### Jekyllのソースを覗く: **\_data/docs.yml**

> ```yaml
> - title: Getting Started
>   docs:
>   - home
>   - quickstart
>   - installation
>   - usage
>   - structure
>   - configuration
> 
> - title: Your Content
>   docs:
> - frontmatter
> …
> ```
> 
> ---
> 
> * [jekyll/docs.yml at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_data/docs.yml)


{% assign variable_path = "Jekyll/site-variables.md" %}
{% assign variable = site.html_pages | where:"path", variable_path | first %}

ぐったりしながらソースを眺めてみると、この **docs.yml** を元に作成しているようだ。
そりゃそうだよなぁ。[ {{variable.title}} ]( {{site.github.url}}{{variable.url}} )で調べた限り、
デフォルトだとただページ一覧とかの情報しかない。これだけだときつい。

じゃあこの **docs.yml** からどうやってジェネレートしているのか。

### Jekyllのソースを覗く: **\_includes/docs\_contents.html**

{% raw %}
> ```liquid
> <div class="unit one-fifth hide-on-mobiles">
>   <aside>
>     {% for section in site.data.docs %}
>     <h4>{{ section.title }}</h4>
>     {% include docs_ul.html items=section.docs %}
>     {% endfor %}
>   </aside>
> </div>
> ```
> 
> ---
> 
> * [jekyll/docs\_contents.html at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_includes/docs_contents.html)

**docs.yml** を走査している`for`があって、まず`title`のデータを用いてヘッダを作っているのが分かる。
その後

```liquid
{% include docs_ul.html items=section.docs %}
```

している。`include`に`items`でページ配列を渡しているぞ？
{% endraw %}

### `include`で引数を渡す

{% raw %}
> ### Passing in parameters
> 
> If you need to pass in parameters you can do that too.
> The fragment below shows how you might pass in a string `"a value"` and a variable `variable_name`.
> 
> ```liquid
> {% include your_function.html some_parameter="a value" another_parameter=variable_name %}
> ```
> 
> Within the file you can access those parameters using `{{ include.some_parameter }}` and `{{ include.another_parameter }}` respectively.
> 
> ---
> 
> * [Using Jekyll \_includes as custom liquid functions](http://hamishwillee.github.io/2014/11/13/jekyll-includes-are-functions/)

**\_includes** ディレクトリ下のファイルをインクルードする際、引数名と値をセットすることで引数を渡すことができる。
渡された引数には`include.value`といった形式でアクセスできるとのこと。

{% endraw %}

### Jekyllのソースを覗く: **\_includes/docs_ul.html**

{% raw %}
> ```liquid
> <ul>
> {% for item in include.items %}
>   {% assign item_url = item | prepend:"/docs/" | append:"/" %}
>   {% assign p = site.docs | where:"url", item_url | first %}
>   <li class="{% if item_url == page.url %}current{% endif %}"><a href="{{ p.url }}">{{ p.title }}</a></li>
> {% endfor %}
> </ul>
> ```
> 
> ---
> 
> * [jekyll/docs\_ul.html at jekyll/jekyll](https://github.com/jekyll/jekyll/blob/master/docs/_includes/docs_ul.html)

```liquid
{% assign item_url = item | prepend:"/docs/" | append:"/" %}
```

受け取った配列からURLを作っている。`item_url`は`/docs/item/`の形の文字列になる。

```liquid
{% assign p = site.docs | where:"url", item_url | first %}
```

え、`site.directory`でディレクトリ指定できるの？

{% endraw %}

> ## Collections
> 
> Not everything is a post or a page. Maybe you want to document the various methods in your open source project,
> members of a team, or talks at a conference. Collections allow you to define a new type of document
> that behave like Pages or Posts do normally, but also have their own unique properties and namespace.
> 
> ---
> 
> 
> Add the following to your site’s `_config.yml` file, replacing `my_collection` with the name of your collection:
> 
> ```yaml
> collections:
> - my_collection
> ```
> 
> ---
> 
> ### Step 2: Add your content
> 
> Create a corresponding folder (e.g. `<source>/_my_collection`) and add documents. 
> YAML front matter is processed if the front matter exists, 
> and everything after the front matter is pushed into the document’s `content` attribute. 
> If no YAML front matter is provided, Jekyll will not generate the file in your collection.
> 
> ---
> 
> ### Step 3: Optionally render your collection’s documents into independent files
> 
> If you’d like Jekyll to create a public-facing, 
> rendered version of each document in your collection,
> set the `output` key to `true` in your collection metadata in your `_config.yml`:
> 
> ```yaml
> collections:
>   my_collection:
>     output: true
> ```
> 
> This will produce a file for each document in the collection. For example,
> if you have `_my_collection/some_subdir/some_doc.md`,
> it will be rendered using Liquid and the Markdown converter of your choice
> and written out to `<dest>/my_collection/some_subdir/some_doc.html`
> 
> ---
> 
> ### Collections
> 
> Each collection is accessible as a field on the `site` variable.
> For example, if you want to access the `albums` collection found in `_albums`,
> you’d use `site.albums`.
> 
> ---
> 
> * [Collections - Jekyll](https://jekyllrb.com/docs/collections/)

これを使っているのか。Collectionは関連するデータをまとめて入れておく場所という認識でよいかな。

使い方は **\_config.yml** に

```yaml
collections:
 - collectionname
```

とCollectionを列挙。それに対応するディレクトリが **\_collectionname** と
先頭に`_`をつけた同名のディレクトリ。

コンテンツをページとして出力したい場合、

```yaml
collections:
 collectionname:
  output : true
```

と`output: true`オプションをつける必要がある。
`output: true`にすると、デフォルトでは

```
_collectionname/test.md
```

は

```
collectionname/test.html
```

に展開される。

ここまで定義しておくと、

`site.collectionname`で中の要素にアクセスできる。データまとめるのに便利だからこれから使っていこうかな。

話を戻してさっき見ていた行をみる

{% raw %}
```liquid
{% assign p = site.docs | where:"url", item_url | first %}
```

`docs` Collectionから`where`でフィルタリングしている。

> Where
> : Select all the objects in an array where the key has the given value.
> : `{{ site.members | where:"graduation_year","2014" }}`
> 
> ---
> 
> * [Templates - Jekyll](https://jekyllrb.com/docs/templates/)

引用した例では`site.members`配列の内、`graduation_year`の要素が`2014`のもののみを抽出しているということになる。

ソース側に当てはめると、`url`要素が`item_url`と合致するもののみを抽出している。
合致したデータが1つだけであろうと抽出したデータは配列として返ってくるので、さらに`first`でフィルタリングして1つだけを取り出し、
`p`に割り当てているということらしい。

つまり、urlを検索キーに使ってページオブジェクトを取得しているようだ。
それ以下のコードは取得したオブジェクトからタイトルとかを取り出している。

{% endraw %}

## Collectionを使うための準備


### `_config.yml`を編集する

```yaml

collections :
  Jekyll:
    output: true

```

Jekyll系の記事のメニューを作ってみる。

### ディレクトリ構成を変更

**\_Jekyll** ディレクトリを作成し、Jekyll関連のページを移動する。

```
|--_Jekyll
|  |--Ruby-research.md
|  |--bundle-usage.md
|  |--display-value.md
|  |--github-pages-debug.md
|  |--github-pages-generator.md
|  |--jekyll-usage.md
|  |--liquid-raw.md
|  |--page-menu.md
|  |--rbenv-install.md
|  |--rbenv-usage.md
|  |--site-variables.md
|  |--worth-bundle-install.md
```

## オブジェクトのキーとなる要素の調査

`site.collection`の中から`where`でフィルタリングしてページオブジェクトを手に入れて、
`title`や`url`を引っ張ってくるのがスマートな気がする。

ので、まずどんなデータが入っているのか調べる。

{% raw %}
```liquid
{{ site.Jekyll | inspect | jsonfy }}
```
{% endraw %}

> {{site.Jekyll | inspect | jsonfy }}

{% raw %}
```liquid
{{ site.Jekyll[0].title | inspect }}
{{ site.Jekyll[0].url   | inspect }}
{{ site.Jekyll[0].path  | inspect }}
```
{% endraw %}

> {{ site.Jekyll[0].title | inspect }}
> {{ site.Jekyll[0].url   | inspect }}
> {{ site.Jekyll[0].path  | inspect }}

データが多すぎるので抜粋した。この辺りをキーにしてオブジェクトを引っ張ってくるのがよさそう。

## メニューを生成するために必要なデータを考える

メニューに必要なものを考える。

* タイトル
* ページへのリンク
* 最終更新日時

ページオブジェクトを取得する必要がありそうなので、
コンパイル前の、こちらでパスを把握している`path`をキーにしてページオブジェクトを取得する。

そのためにファイル名の一覧をメニュー構成用データに使う。

```yaml
- title  : github pages & Jekyll
  prefix : Jekyll
  pages  : 
  - Ruby-research
  - bundle-usage
  - github-pages-debug
  - github-pages-generator
  - jekyll-usage
  - rbenv-install
  - rbenv-usage
  - worth-bundle-install
  - site-variables
  - liquid-raw
  - display-value
```

複数のカテゴリを横断する記事はファイル名以下に何かプロパティをぶら下げて区別しようかな。
**\_data/pages.yml** として保存した。

## ページ一覧から`path`を生成する。

{% raw %}

> Combining multiple strings into one variable:
> 
> ```liquid
> {% capture full-name %}{{ name }} {{ surname }}{% endcapture %}
> ```
> 
> ---
> 
> * [Jekyll & Liquid Cheat Sheet](https://gist.github.com/smutnyleszek/9803727)
{% endraw %}

これを使うとパスを作るのが簡単そうだった。

ページ一覧を`for`で走査して`capture`でパスを作り出す。

{% raw %}
```liquid
{% for section in site.data.pages %}
  {% for page in section.pages %}
    {% capture path %}_{{ section.prefix }}/{{page}}.md{% endcapture %}
    {{path}}
  {% endfor %}
{% endfor %}

```
{% endraw %}

> {% for section in site.data.pages %}
>   {% for page in section.pages %}
>     {% capture path %}_{{ section.prefix }}/{{page}}.md{% endcapture %}
> {{path}}
>   {% endfor %}
> {% endfor %}


## パスを使ってページタイトル、URLを取得してみる

{% raw %}

では、パスをキーにしてオブジェクトを取得、タイトルとURLを取得してみる。

```liquid
{% for section in site.data.pages %}
  {% for page in section.pages %}
  {% capture path %}_{{ section.prefix }}/{{page}}.md{% endcapture %}
    <p>
    {% assign content = site.Jekyll | where:"path", path | first %}
    {{content.title}}
    </p>
    <p>
    {{content.url}}
    </p>
  {% endfor %}
{% endfor %}
```
{% endraw %}

> {% for section in site.data.pages %}
>   {% for page in section.pages %}
>   {% capture path %}_{{ section.prefix }}/{{page}}.md{% endcapture %}
> <p>
> {% assign content = site.Jekyll | where:"path", path | first %}
> {{content.title}}
> </p>
> <p>
> {{content.url}}
> </p>
>   {% endfor %}
> {% endfor %}

うまくいっているようだ。
