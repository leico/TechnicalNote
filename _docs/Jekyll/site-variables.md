---
layout : post
title  : site変数の内容調査
date   : 2017/03/03
lastchange : 2017-06-16 23:13:15.
tags   :
  - jekyll
  - liquid
  - github
  - pages
---

## `site.*`内の変数についての調査

Jekyllの変数に格納されているものがよくわからないので調べた。

参考

* [Variables - Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/variables/)
* [Jekyllで簡易タグ一覧ページを作る（2013年12月8日追記）](http://genjiapp.com/blog/2013/11/21/simple-tags-page-for-jekyll.html)

## `site.posts`変数の中身


{% capture text %}
`site.posts`
: A reverse chronological list of all Posts.
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Variables - Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/variables/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




ということなので、**\_post** 以下のものが日付が新しい順に表示されるはずだ。

{% raw %}
```liquid
<ul>
  {% for post in site.posts %}
  <li>
    {{post.date | date: "%F" }}:<a href="{{site.github.url}}{{ post.url }}">{{ post.title }}</a>
  </li>
  {% endfor %}
</ul>
```
{% endraw %}

これを実行してみる。

> 
> <ul>
>   {% for post in site.posts %}
>   <li>
>     {{post.date | date: "%F" }}:<a href="{{site.github.url}}{{ post.url }}">{{ post.title }}</a>
>   </li>
>   {% endfor %}
> </ul>
>

## `site.pages`変数の中身

{% capture text %}
`site.pages`
: A list of all Pages. 
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Variables - Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/variables/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



全てのページが出力されるらしい。先程と同じようなコードを実行してみる。

{% raw %}
```liquid
<ul>
  {% for page in site.pages %}
  <li>
    {{ page.url }}
  </li>
  
  {% endfor %}
</ul>
```
{% endraw %}

実行してみる。

> <ul>
>   {% for page in site.pages %}
>   <li>
>     {{ page.url }}
>   </li>
>   
>   {% endfor %}
> </ul>

cssやxmlなど、必要そうな不要そうなものまで出力される。

## `site.html_pages`変数の中身


{% capture text %}
`site.html_pages`
: A subset of `site.pages` listing those which end in `.html`. 
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Variables - Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/variables/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




どうやら同じような考えだったらしく、**html** だけ格納されている変数があった。

{% raw %}
```liquid
<ul>
  {% for page in site.html_pages %}
  <li>
    {{page.date | date: "%F"}}:<a href="{{site.github.url}}{{ page.url }}">{{ page.title }}</a>
  </li>
  {% endfor %}
</ul>
```
{% endraw %}

実行してみる。

> <ul>
>   {% for page in site.html_pages %}
>   <li>
>     {{page.date | date: "%F"}}:<a href="{{site.github.url}}{{ page.url }}">{{ page.title }}</a>
>   </li>
>   {% endfor %}
> </ul>

トップページまで出力されるのか・・・。



## 記事のタグ一覧と、タグ付けされているページの一覧を取得する。

{% capture text %}
`site.tags.TAG`
: The list of all Posts with tag TAG. 
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Variables - Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/variables/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}

{% raw %}
```liquid
{% for tag in site.tags %}
<article>
  <h1 id="tag_{{ tag[0] }}">{{ tag[0] }}</h1>
  <ul>
    {% for post in tag[1] %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
</article>
{% endfor %}
```
{% endraw %}

{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
[Jekyllで簡易タグ一覧ページを作る（2013年12月8日追記）](http://genjiapp.com/blog/2013/11/21/simple-tags-page-for-jekyll.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



上記ソースでは`tag[0]`がタグ名、`tag[1]`がタグを含むページの配列になっているらしい。
少し変更して実行してみる。

{% raw %}
```liquid
{% for tag in site.tags %}
<article>
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
</article>
{% endfor %}
```
{% endraw %}

実行してみる。

> {% for tag in site.tags %}
> <article>
>   <h3>{{ tag[0] }}</h3>
>   <ul>
>     {% for post in tag[1] %}
>     <li><a href="{{ post.url }}">{{ post.title }}</a></li>
>     {% endfor %}
>   </ul>
> </article>
> {% endfor %}

**{{ page.lastchange }} 現在、固定ページのタグは取得できない** ことがわかった。

