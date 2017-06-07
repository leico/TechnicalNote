---
layout : post
title  : Jekyll:postsのレイアウトについて考える
date   : 2017/02/03
lastchange : 2017-03-05 17:18:19.
tags   :
  - bootstrap
  - jekyll
---

## さすがにみづらい

post用のレイアウトを作ってなかったがさすがにみづらいので仮で作った。

参考:

* [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)
* [Components - Bootstrap#Page header](http://getbootstrap.com/components/#page-header)
* [CSS - Bootstrap#description](http://getbootstrap.com/css/#description)
* [date – Liquid template language](https://shopify.github.io/liquid/filters/date/)

結果として今はこんな感じ

{% raw %}

```html
---
layout : default
---

<header class="page-header">
  <h1>{{page.title}}</h1>
  <dl class="dl-horizontal">
    <dt>created</dt>
    <dd>{{page.date | date : "%F"}}</dd>
    <dt>last change</dt>
    <dd>{{page.lastchange}}</dd>
  </dl>
</header>

<div class="row">
  {{ content }}
</div>
```

{% endraw %}


過程は以下

## Jekyllでpost用のレイアウトを作る

{% raw %}

> ### Postのレイアウト
> 
> 次に記事にも専用のレイアウトを用意します。\_layoutsディレクトリ以下に`post.html`を作って、次のような内容にします。
> 
> ```sh
> % touch _layouts/post.html
> ```
> 
> ```html
> ---
> layout: default
> ---
> <header>
>   <div>{{ page.date | date_to_string }}</div>
>   <h1>{{ page.title }}</h1>
> </header>
> 
> <div class='main'>
>   {{ content }}
> </div>
> 
> <footer>
>   <p>- rendered with post template -</p>
> </footer>
> ```
> 
> Front-Matterでdefault.htmlを読み込むようにします。これによりpost.htmlはdefault.htmlのサブテンプレートになります。header部分に投稿日とタイトルを表示するようLiquidで指定します。
> 
> なお、`{{ page.date | date_to_string }}`はJekyllで拡張されたLiquidのフィルターという機能を使って、
> 出力の整形を行なっています([Templates](http://jekyllrb.com/docs/templates/))。
> default.htmlと同様に、このテンプレートが読み込まれたことを見るために __rendered with post template__ を追加しておきます。
> 
> --- 
> 
> * [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

{% endraw %}

これを参考にbootstrap風味にする。

> # Page header
> 
> A simple shell for an h1 to appropriately space out and segment sections of content on a page.
> It can utilize the h1's default small element, as well as most other components (with additional styles).
> 
> <div class="page-header">
>   <h1>Example page header <small>Subtext for header</small></h1>
> </div>
> 
> ```html
> <div class="page-header">
>   <h1>Example page header <small>Subtext for header</small></h1>
> </div>
> ```
> 
> ---
> 
> * [Components - Bootstrap#Page header](http://getbootstrap.com/components/#page-header)

__page-header__ クラスがあるので利用する。

他に作成日時と、変更日時を独自に仕込んでいるので入れたい。

> #### Horizontal description
> 
> Make terms and descriptions in `<dl>` line up side-by-side.
> Starts off stacked like default `<dl>`s, but when the navbar expands, so do these.
> 
> <dl class="dl-horizontal">
>   <dt>Description lists</dt>
>   <dd>A description list is perfect for defining terms.</dd>
>   <dt>Euismod</dt>
>   <dd>
>     Vestibulum id ligula porta felis euismod semper eget lacinia odio sem nec elit.
>     Donec id elit non mi porta gravida at eget metus.
>   </dd>
>   <dt>Malesuada porta</dt>
>   <dd>Etiam porta sem malesuada magna mollis euismod.</dd>
>   <dt>Felis euismod semper eget lacinia</dt>
>   <dd>
>     Fusce dapibus, tellus ac cursus commodo,
>     tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.
>   </dd>
> </dl>
> 
> ```html
> <dl class="dl-horizontal">
>   <dt>...</dt>
>   <dd>...</dd>
> </dl>
> ```
> 
> ---
> 
> * [CSS - Bootstrap#description](http://getbootstrap.com/css/#description)

{% raw %}

> # date
> 
> Converts a timestamp into another date format.
> The format for this syntax is the same as [strftime](http://strftime.net/).
> 
> ### Input
> 
> ```
> {{ article.published_at | date: "%a, %b %d, %y" }}
> ```
> 
> ### Output
> 
> ```
> Fri, Jul 17, 15
> ```
> 
> ---
> 
> * [date – Liquid template language](https://shopify.github.io/liquid/filters/date/)

{% endraw %}

定義リストとこの書式制御を利用することにした。
