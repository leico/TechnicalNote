---
layout : post
title  : Ableton Push2でのM4Lパラメータ表示順序
date   : 2017/04/19
lastchange : 2017-06-16 23:25:44.
tags   :
  - Ableton
  - Live
  - 9
  - Max for Live
  - M4L
  - automapping index
---

## デフォルトの表示順序だととても使いにくい

何ら指定することなくMax for Liveデバイスを作ると

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/automapping-index/01_4bandeq.png %}{% endcapture %}
{% assign caption = 'これが' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/automapping-index/02_mapping.jpg %}{% endcapture %}
{% assign caption = 'こう表示されてとっても使いにくい' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

で、Ableton Supportに聞いてみた。

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/automapping-index/03_support.png %}{% endcapture %}

{% capture caption %}

{% capture text %}
To change the order of the controls on Push you'd have to define the Automapping Index parameter within the Max Inspector.
 
As soon as you assign a different value than 0 to a parameter you'd enable the functionality for all other parameters as well. 
Practically this means 0 becomes not available to Push and 1, 2, 3... defines the order on the Push display.

Please make sure to cycle tracks to make changes visible on Push after changing/saving the Max patch.
 
Let me know if you have further questions and feel free to come back at any time!
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
Ableton Support
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% endcapture %}
{% assign caption=caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

> インスペクタ内のAutomapping Index の値が小さい方から順番に並ぶよー

マジか！！！

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/automapping-index/04_autoindex.jpg %}{% endcapture %}
{% assign caption = 'マジだった' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

{% capture url %}{{ site.github.url }}{% link _docs/Live/images/automapping-index/05_skip.jpg %}{% endcapture %}
{% assign caption = '
数字を飛ばすとその分空白ができる。
' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

このマッピングは

1. Low
2. Low Kill
3. Low\_Mid
4. Low\_Mid Kill
5. High\_Mid
6. High\_Mid Kill
7. High
8. High Kill
9. 
10. Low crossover
11. 
12. 
13. Mid crossover
14. 
15. High crossover

をやってみた時
