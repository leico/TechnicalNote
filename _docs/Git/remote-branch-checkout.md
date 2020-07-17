---
layout : post
title  : "remote のブランチを checkout する"
date   : 2020/07/17
lastchange : 2020-07-18 02:14:45
tags   :
  - git
  - remote
  - checkout
---

## remote ブランチを checkout する方法を毎回忘れる

ので忘備録で書いておく

参考:

* [gitでリモートブランチをローカルにcheckoutする - Qiita](https://qiita.com/YusukeSuzuki@github/items/3bd5752783fd2c2f8805 "gitでリモートブランチをローカルにcheckoutする - Qiita")

## remote ブランチを checkout する

{% capture text %}

新しい作業環境で開発リポジトリのmasterリポジトリをcloneしたあとにその他のブランチもcheckout/pullしたいことがある。というか、しないと作業にならない。

よく忘れるのでメモ。

```sh
git checkout -b local_branch_name origin/remote_branch_name
```

remote_branch_nameリモートブランチから派生するlocal_branch_nameローカルブランチを新たに作成する。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[gitでリモートブランチをローカルにcheckoutする - Qiita](https://qiita.com/YusukeSuzuki@github/items/3bd5752783fd2c2f8805 "gitでリモートブランチをローカルにcheckoutする - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

 `git branch` コマンド系で同様の操作ができないか調べたが、そちらは見つからなかった。