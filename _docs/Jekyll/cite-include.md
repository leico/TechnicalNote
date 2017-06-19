---
layout : post
title  : Jekyll & Bootstrapで引用の書きかた
date   : 2017/06/20
lastchange : 2017-06-20 01:02:25.
tags   :
  - jekyll
  - github
  - pages
  - bootstrap4
  - blockquote
  - cite
---

## 引用元をmarkdownで書きたいけど難しい

なかなかいい感じの記法が思いつかなくて、今までは

```
> こんな
> かんじ
>
> ---
>
> * 引用元

```

> こんな
> かんじ
>
> ---
>
> * 引用元

としていた。

## Bootstrapでの引用の書きかた

{% assign text='

```html
<blockquote class="blockquote">
  <p class="mb-0">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
  <footer class="blockquote-footer">Someone famous in <cite title="Source Title">Source Title</cite></footer>
</blockquote>
```

<blockquote class="blockquote">
  <p class="mb-0">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer posuere erat a ante.</p>
  <footer class="blockquote-footer">Someone famous in <cite title="Source Title">Source Title</cite></footer>
</blockquote>


' | markdownify %}
{% assign source='
[Typography · Bootstrap](https://v4-alpha.getbootstrap.com/content/typography/#naming-a-source)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このように書く。見た目以外にちゃんとしたタグを利用していてよいのでこれを実現したい。

## Jekyll の include に引数を渡すことができる

{% capture text %}
### Passing parameters to includesPermalink

You can also pass parameters to an include. For example, suppose you have a file called `note.html` in your `_includes` folder that contains this formatting:

{% raw %}
```html
<div markdown="span" class="alert alert-info" role="alert">
<i class="fa fa-info-circle"></i> <b>Note:</b>
{{ include.content }}
</div>
```

The `{{ include.content }}` is a parameter that gets populated 
when you call the include and specify a value for that parameter, like this:

```liquid
{% include note.html content="This is my sample note." %} 
```

The value of `content` (which is `This is my sample note`) will be inserted 
into the `{{ include.content }}` parameter.
{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Includes \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/includes/#passing-parameters-to-includes)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% raw %}
Jekyll の `include` には引数を渡すことができ、include 内部では
その引数に `{{ include.value }}` のようにアクセスすることができる。
{% endraw %}

これを利用すればbootstrap式の引用文ができそう。

## 引用文向けのテンプレート

{% raw %}
```liquid
{% capture text %}
こんな

かんじ
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
引用元
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}
```
{% endraw %}

{% capture text %}
こんな

かんじ
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
引用元
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

とした。

{% capture text %}
{% raw %}
## capture

Captures the string inside of the opening and closing tags and assigns it to a variable. 
Variables that you create using capture are stored as strings.

Using capture, you can create complex strings using other variables created with assign.

Input
: ```liquid
  {% assign favorite_food = 'pizza' %}
  {% assign age = 35 %}

  {% capture about_me %}
  I am {{ age }} and my favorite food is {{ favorite_food }}.
  {% endcapture %}

  {{ about_me }}
  ```

Output
: ```
  I am 35 and my favorite food is pizza.
  ```

{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Variable tags - Tags - Shopify Help Center](https://help.shopify.com/themes/liquid/tags/variable-tags#capture)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

引用元文章を markdown で、かつ `'`や `"` といった特殊文字をエスケープする必要がないように、

{% raw %}
```liquid
{% capture text %}...{% endcapture %}
```
{% endraw %}

で文章を取得後

{% capture text %}
{% raw %}
Markdownify
: Convert a Markdown-formatted string into HTML.
: 
: `{{ page.excerpt | markdownify }}`

{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Templates \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/templates/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% raw %}
```liquid
{% assign text=text | markdownify %}
```
{% endraw %}

`markdownify` フィルタでhtmlへ変換している。

引用元の方は`markdownify`の出力が

```html
<p>
  <cite>引用元</cite>
</p>
```

と余分な`<p>` タグが入ってしまうため、直前で`<p>`タグを消去している。

{% capture text %}
{% raw %}
## remove

Removes all occurrences of a substring from a string.

Input
: ```liquid
  {{ "Hello, world. Goodbye, world." | remove: "world" }}
  ```
Output
: ```
  Hello, . Goodbye, .
  ```
{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[String filters - Filters - Shopify Help Center](https://help.shopify.com/themes/liquid/filters/string-filters#remove)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

こんな

{% capture text2 %}

ふうに

{% capture text3 %}

孫引きのような引用も

{% capture text4 %}

可能

{% endcapture %}
{% assign text4=text4 | markdownify %}
{% capture source4 %}
引用元4
{% endcapture %}
{% assign source4=source4 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text4 source=source4 %}


{% endcapture %}
{% assign text3=text3 | markdownify %}
{% capture source3 %}
引用元3
{% endcapture %}
{% assign source3=source3 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text3 source=source3 %}


{% endcapture %}
{% assign text2=text2 | markdownify %}
{% capture source2 %}
引用元2
{% endcapture %}
{% assign source2=source2 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text2 source=source2 %}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
引用元1
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% raw %}
```liquid
{% capture text %}

こんな

{% capture text2 %}

ふうに

{% capture text3 %}

孫引きのような引用も

{% capture text4 %}

可能

{% endcapture %}
{% assign text4=text4 | markdownify %}
{% capture source4 %}
引用元4
{% endcapture %}
{% assign source4=source4 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text4 source=source4 %}


{% endcapture %}
{% assign text3=text3 | markdownify %}
{% capture source3 %}
引用元3
{% endcapture %}
{% assign source3=source3 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text3 source=source3 %}


{% endcapture %}
{% assign text2=text2 | markdownify %}
{% capture source2 %}
引用元2
{% endcapture %}
{% assign source2=source2 | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text2 source=source2 %}

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
引用元1
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}
```
{% endraw %}
