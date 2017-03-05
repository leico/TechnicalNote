---
layout : post
title  : liquidのソースコードを表示する
date   : 2017/03/03
lastchange : 2017-03-04 00:06:21.
tags   :
  - jekyll
  - liquid
  - github
  - pages
---

## liquidのソースがJekyllでコンパイルされ、表示されない

liquidのソースコードを載っけようとしたところ、
Jekyllでコンパイルする際に展開されてしまい、
表示されず、意図しないものが出力されてしまう。

参考

* [How to escape liquid template tags? - Stack Overflow](http://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags)

## liquidのソースコードをエスケープする方法

> it is possible to disable liquid processing engine using the raw tag:
> 
> ```liquid
> {{ "{% raw " }} %}
> {% raw %}
> {% this %}
> {% endraw %}
> {{ "{% endraw " }} %}
> ```
> 
> will display 
> {% raw %}
> ```liquid
> {% this %}
> ```
> {% endraw %} 
> by [Etienne](http://stackoverflow.com/users/406458/etienne)
> 
> 
> ---
> 
> * [How to escape liquid template tags? - Stack Overflow](http://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags)


ということで`{{ "{% raw "}} %}`と`{{ "{% endraw " }} %}`で挟んだ間は展開されない。

## `{{ "{% raw "}} %}`と`{{ "{% endraw " }} %}`をエスケープする

だがしかし、`{{ "{% raw "}} %}`と`{{ "{% endraw " }} %}`を表示させることはこれではできない。
そこで、上記の文はこのようになっている。

{% raw %}
```liquid
ということで`{{ "{% raw "}} %}`と`{{ "{% endraw " }} %}`で挟んだ間は展開されない。
```
{% endraw  %}
