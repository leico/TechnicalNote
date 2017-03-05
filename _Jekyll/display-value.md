---
layout : post
title  : 変数の中身をページに表示する
date   : 2017/03/03
lastchange : 2017-03-04 00:33:25.
tags   :
  - jekyll
  - liquid
  - github
  - pages
---

## 変数に何が入っているか調べたい

参考

* [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)
* [Templates - Jekyll](https://jekyllrb.com/docs/templates/)


> Jekyllでは生成されたサイトに対する情報は、グローバル変数siteを通してアクセスできます。
> siteがどんな情報を持っているか、ちょっと見てみます。index.mdに戻って、次の一行を追加します。
> 
> {% raw %}
> ```
> ---
> layout: default
> title: Oh! My JK
> ---
> #Welcome to my JK Home Page!
> 
> {{ site }}  <!-- ここを追加 -->
> ```
> {% endraw %}
> 
> ---
> 
> * [Jekyllいつやるの？ジキやルの？今でしょ！](http://melborne.github.io/2013/05/20/now-the-time-to-start-jekyll/)

これが動かなかったので、変数をDumpする方法を調べた

## inspectフィルタ

{% raw %}
> Inspect
> : Convert an object into its String representation for debugging.
> : `{{ some_var | inspect }}`
> 
> ---
> 
> * [Templates - Jekyll](https://jekyllrb.com/docs/templates/)
{% endraw %}

とのことで、`inspect`フィルタで表示されるらしい。

{% raw %}
```liquid
{{ page | inspect }}
```
{% endraw %}

実行してみる

> {{ page | inspect }}
