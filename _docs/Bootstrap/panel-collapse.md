---
layout : post
title  : Panelを使ったCollapseやAccordionメニュー
date : 2017/03/5
lastchange : 2017-06-10 18:28:45.
tags   :
  - bootstrap
  - card
  - panel
  - collapse
  - accordion
---

## 記事一覧をアコーディオンメニューにしたらよさそうな気がした

ので、bootstrapのアコーディオンメニューの実装等を調べた。

参考:

* [Bootstrap4 Cards](https://v4-alpha.getbootstrap.com/components/card/)
* [Accordion Example Javascript Bootstrap](http://getbootstrap.com/javascript/#collapse-example-accordion)
* [Collapse · Bootstrap4](https://v4-alpha.getbootstrap.com/components/collapse/)
* [role属性を正しく設定してアクセシビリティを高める – カラクリ.jp](http://xn--lcki7of.jp/502/)
* [Data Attributes Collapse Bootstrap4](https://v4-alpha.getbootstrap.com/components/collapse/#via-data-attributes)
* [aria-expanded属性 \| WAI-ARIA - HTMLリファレンス](http://www.webcreativepark.net/html/wai-aria/aria-expanded/)
* [WAI-ARIAを意識したタブパネルのマークアップを考えてみる【アクセシビリティ】【HTML5】 - E-riverstyle Vanguard](http://blog.e-riverstyle.com/2011/01/waiariahtml5.html)
* [aria-labelledby属性 \| WAI-ARIA - HTMLリファレンス](http://www.webcreativepark.net/html/wai-aria/aria-labelledby/)
* [Bootstrap3 アコーディオンとCollapseの簡単な使い方](http://tetra-themes.com/bootstrap3-collapse-194/)

## Cardの使い方

> If you’re familiar with Bootstrap 3,
> cards replace our old panels, wells, and thumbnails.
> Similar functionality to those components is available as modifier classes for cards.
> 
> ---
> 
> * [Bootstrap4 Cards](https://v4-alpha.getbootstrap.com/components/card/)

Bootstrap4ではPanelはCardに置き換わるらしい。

> ## Basic example
> 
> By default, all the `.panel` does is apply some basic border and padding to contain some content.
> 
> ```html
> <div class="card">
>   <div class="card-block">
>     This is some text within a card block.
>   </div>
> </div>
> ```
>
> <div class="card">
>   <div class="card-block">
>     This is some text within a card block.
>   </div>
> </div>
> 
> ---
>
> * [Bootstrap4 Cards](https://v4-alpha.getbootstrap.com/components/card/)

`<div class="card">`で囲えばパネルになるらしい。
パネルのコンテンツは内部の`<div class="card-block">`タグ内に記述するようだ。
他に、`<div class="card-header">`を先頭に書くとヘッダが出力され、
`<div class="card-footer">`を最後に書くとフッターが出力されるようだ。

## アコーディオン用のパネルテンプレ

このような感じにした。

```html
<div class="col-md-4">
  <div class="card">
    <h3 class="card-header">
      title
    </h3>
    <div class="card-block">
      contents here
    </div>
  </div>
</div>
```

<div class="row">
  <div class="col-md-4">
    <div class="card">
      <h3 class="card-header">
        title
      </h3>
      <div class="card-block">
        contents here
      </div>
    </div>
  </div>
</div>

将来のことを考え、一緒に`card`クラスも指定している。

階層が深くてやな感じなので`<div class="col-md-4">`と
`<div class="card">`を一緒にできないかとおもったが、
デザインが崩れたのでもとに戻した。

```html
<div class="row">
  <div class="col-md-4 card">
    <h3 class="card-header">
      ←なんやねんこれ→
    </h3>
    <div class="panel-body card-block">
      contents here
    </div>
  </div>
</div>
```

<div class="row">
  <div class="col-md-4 card">
    <h3 class="card-header">
      ←なんやねんこれ→
    </h3>
    <div class="panel-body card-block">
      contents here
    </div>
  </div>
</div>

## Collapse機能を追加する

> ```html
> <div class="panel panel-default">
>     <div class="panel-heading" role="tab" id="headingOne">
>       <h4 class="panel-title">
>         <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
>           Collapsible Group Item #1
>         </a>
>       </h4>
>     </div>
>     <div id="collapseOne" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="headingOne">
>       <div class="panel-body">
>         Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry richardson ad squid.
>         3 wolf moon officia aute, non cupidatat skateboard dolor brunch. Food truck quinoa nesciunt laborum eiusmod.
>         Brunch 3 wolf moon tempor, sunt aliqua put a bird on it squid single-origin coffee nulla assumenda shoreditch et.
>         Nihil anim keffiyeh helvetica, craft beer labore wes anderson cred nesciunt sapiente ea proident.
>         Ad vegan excepteur butcher vice lomo. Leggings occaecat craft beer farm-to-table, 
>         raw denim aesthetic synth nesciunt you probably haven't heard of them accusamus labore sustainable VHS.
>       </div>
>     </div>
>   </div>
> ```
> 
> <div class="panel panel-default">
>   <div class="panel-heading" role="tab" id="headingOne">
>     <h4 class="panel-title">
>       <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
>         Collapsible Group Item #1
>       </a>
>     </h4>
>   </div>
>   <div id="collapseOne" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="headingOne">
>     <div class="panel-body">
>       Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry richardson ad squid.
>       3 wolf moon officia aute, non cupidatat skateboard dolor brunch. Food truck quinoa nesciunt laborum eiusmod.
>       Brunch 3 wolf moon tempor, sunt aliqua put a bird on it squid single-origin coffee nulla assumenda shoreditch et.
>       Nihil anim keffiyeh helvetica, craft beer labore wes anderson cred nesciunt sapiente ea proident.
>       Ad vegan excepteur butcher vice lomo. Leggings occaecat craft beer farm-to-table, 
>       raw denim aesthetic synth nesciunt you probably haven't heard of them accusamus labore sustainable VHS.
>     </div>
>   </div>
> </div>
> 
> ---
> 
> * [Accordion Example Javascript Bootstrap](http://getbootstrap.com/javascript/#collapse-example-accordion)

こうすればCollapse機能が実装されるらしいが、

```html
<div class="panel-heading" role="tab" id="headingOne">
<a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
<div id="collapseOne" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="headingOne">
```

タグの中に知らないものが多すぎる。あとやっぱり階層深すぎ。

>  *role 属性* は、要素が示す役割を明確にするための属性です。
>  例えば、`header` 要素はコンテンツの `header` か、あるいはサイト全体の `header` を示すことなどができますが、
>  どちらの `header` であるかを明確に区別する手段はありません。 
>  しかしながら、*role 属性* の値 `banner` によって、コンテンツ固有の `header` ではなく、サイトの持つ `header` であることを示すことができます。
> 
> ---
> 
> * [role属性を正しく設定してアクセシビリティを高める – カラクリ.jp](http://xn--lcki7of.jp/502/)

*role* はより詳細な役割を示すための属性。


> ### Via data attributes
> 
> Just add `data-toggle="collapse"` and a `data-target` to the element to automatically assign 
> control of a collapsible element. The `data-target` attribute accepts a CSS selector 
> to apply the collapse to. Be sure to add the class `collapse` to the collapsible element.
> If you’d like it to default open, add the additional class `show`.
> 
> ---
> 
> * [Data Attributes Collapse Bootstrap4](https://v4-alpha.getbootstrap.com/components/collapse/#via-data-attributes)

`data-toggle="collapse"`と`data-target`を指定することで、クリックがなされた時に自動的に`data-target`が開閉されるようになる。
余計なJSを書く必要がない。

> To add accordion-like group management to a collapsible control,
> add the data attribute `data-parent="#selector"`. Refer to the demo to see this in action.
> 
> ---
> 
> * [Data Attributes Collapse Bootstrap4](https://v4-alpha.getbootstrap.com/components/collapse/#via-data-attributes)

アコーディオンメニューで連動して他のメニューを閉じたい場合、
`data-parent`属性でアコーディオンメニューを格納している親要素を指定する必要がある。

> aria-expanded属性は要素の開閉の状態を示すためのWAI-ARIAの属性です。
> 
> 値にはtrue/falseのboolean値を指定することができtrue(開いている状態)、false(閉じている状態)を示すことができます。
> 
> ---
> 
> * [aria-expanded属性 \| WAI-ARIA - HTMLリファレンス](http://www.webcreativepark.net/html/wai-aria/aria-expanded/)

> ## WAI-ARIA
> 
> WAI-ARIAとは、Web Accessibility Initiative　Accessible Rich Internet Applicationsの略で、WAI-ARIA Overviewから引用すると...
> 
> > WAI-ARIA, the Accessible Rich Internet Applications Suite, 
> > defines a way to make Web content and Web applications more accessible to people with disabilities.
> > It especially helps with dynamic content and advanced user interface controls developed with Ajax, HTML, JavaScript, and related technologies.
> 
> 訳すと、WebコンテンツおよびWebアプリケーションを障がいのあるユーザにとってよりアクセシブルにする方法を定義するもので、
> 特にAjaxやJavaScriptなどで作られたRIA（Rich Internet Applications）にフォーカスしたものです。
> 
> ---
> 
> aria-controls
> : 現在の要素によってコンテンツまたは存在が制御されている要素を特定する。（例：ツリービュー、チェックボックス、タブ）
> 
> ---
> 
> * [WAI-ARIAを意識したタブパネルのマークアップを考えてみる【アクセシビリティ】【HTML5】 - E-riverstyle Vanguard](http://blog.e-riverstyle.com/2011/01/waiariahtml5.html)

> **aria-labelledby属性** は要素とラベルを関連付けるWAI-ARIA属性です。
> 
> 利用方法は[label要素](http://www.webcreativepark.net/html/form/label/)に対する
> [for属性](http://www.webcreativepark.net/html/attribute/for/)と同じで、
> 関連付けたい要素の[id属性](http://www.webcreativepark.net/html/global-attributes/id/)を
> **aria-labelledby属性** の値として指定します。
> 
> ---
> 
> * [aria-labelledby属性 \| WAI-ARIA - HTMLリファレンス](http://www.webcreativepark.net/html/wai-aria/aria-labelledby/)

諸々機能的には問題ないが、ラベリングしておいた方がアクセシビリティが高まるようなので利用しましょうということになった in 脳内会議。

ということで、これらを実装した結果

```html
<div class="col-md-4" id="id_panel">
  <div class="card">
    <h3 class="card-header" role="tab" id="head_panel">
      <a href="#body_panel" 
         role="button" 
         data-toggle="collapse"
         data-parent="#id_panel" 
         area-expanded="false" 
         area-controls="body_panel">
        title
      </a>
    </h3>
    <div class="card-block collapse"
         role="tabpanel"
         area-labelledby="head_panel"
         id="body_panel">
         contents
    </div>
  </div>
</div>
```

<div class="row">
  <div class="card">
    <h3 class="card-header" role="tab" id="head_panel">
      <a href="#body_panel" 
         role="button" 
         data-toggle="collapse"
         data-parent="#id_panel" 
         area-expanded="false" 
         area-controls="body_panel">
        title
      </a>
    </h3>
    <div class="card-block collapse"
         role="tabpanel"
         area-labelledby="head_panel"
         id="body_panel">
         contents
    </div>
  </div>
</div>

こうなった。
