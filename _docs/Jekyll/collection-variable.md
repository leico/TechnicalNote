---
layout : post
title  : Collection変数を調査する
date   : 2017/03/05
lastchange : 2017-08-13 15:43:34.
tags   :
  - jekyll
  - github
  - pages
  - collection
---


## Collectionの中身を調査する。

{% capture text %}
The collections are also available under `site.collections`,
with the metadata you specified in your `_config.yml` (if present)
and the following information:

| Variable             | Description                                        |
| -------------------- | -------------------------------------------------- |
| `label`              | The name of your collection, e.g. `my_collection`. |
| `docs`               | An array of documents.                             |
| `files`              | An array of static files in the collection.        |
| `relative_directory` | The path to the collection's source directory, relative to the site source. |
| `directory`          | The full path to the collections's source directory. |
| `output`             | Whether the collection's documents will be output as individual files. |
{% endcapture %}
{% assign text = text | markdownify %}
{% assign source='
[Collections - Jekyll](https://jekyllrb.com/docs/collections/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



JekyllのCollectionは `site.collections` に全て格納されるらしいので、
格納されているデータを調査した。

ただし、 **docs** 、**files** は量が多いのでひとつだけ出力するようにしている。

{% raw %}
```liquid
{{site.collections[0].label | inspect }}
{{site.collections[0].docs[0]  | inspect }}
{{site.collections[0].files[0] | inspect }}
{{site.collections[0].relative_directory | inspect }}
{{site.collections[0].direcotory | inspect }}
{{site.collections[0].output | inspect }}
```
{% endraw %}

> {{site.collections[0].label | inspect }}
> 
> {{site.collections[0].docs[0]  | inspect }}
> 
> {{site.collections[0].files[0] | inspect }}
> 
> {{site.collections[0].relative_directory | inspect }}
> 
> {{site.collections[0].direcotory | inspect }}
> 
> {{site.collections[0].output | inspect }}

docsが`site.html_pages`や`site.posts`のようなものになるらしい。
各々のCollectionを区別する方法は`label`や`relative_directory`を用いるのがよさそう。
