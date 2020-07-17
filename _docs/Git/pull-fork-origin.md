---
layout : post
title  : "fork元の変更を取り込む"
date   : 2020/07/17
lastchange : 2020-07-18 02:18:15
tags   :
  - git
  - remote
  - upstream
---

## PR 投げる前に本家の変更を取り込んでおきたい。

ということが github を使っているとそれなりにある。本家 github の変更を取り込む方法を調べた。

参考:

* [GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita](https://qiita.com/xtetsuji/items/555a1ef19ed21ee42873 "GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita")


## upsteam として fork 元のリポジトリを登録してしまう

{% capture text %}
リモートリポジトリとして、オリジナルのリポジトリを **upstream** という名前で設定します。

```sh
$ git remote add upstream git://github.com/DQNEO/Renshu.git
```

以後、このリポジトリは **upstream** という名前で本家リポジトリを参照します。

ブランチを確認すると、以下のように remotes/upstream/master が加わったことが分かります。
出てこない場合には `git fetch upstream` を試した後に再度 `git branch -a` で確認してみてください。

```sh
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/upstream/master
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita](https://qiita.com/xtetsuji/items/555a1ef19ed21ee42873 "GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

普段は origin として自分のリポジトリが登録されており、
そのリポジトリに対して `push` や `pull` を行っている。

{% capture url %}{{site.github.url}}{% link _docs/Git/images/pull-fork-origin/01_default.svg %}{% endcapture %}
{% capture caption %}
[source](http://www.plantuml.com/plantuml/uml/POyzQiD044RxESLSO9ka2q4e47A1d619Hfj5-oDcPvly5EaCRjmQtBiGo9LFP5XVmrj8sE1ovhrNV6qIpPAz0n0hZh1ifp8TJ_zJsQ_BQWEGUXi6pThZAtKwEtcBGTxjD-7Z0m3JX-XWQiZaen3-06AIoDeGHhR61lY3vP6794pQtsJdyL1suml0mb3zDdE9JzX5xW7c4_5PARKabHehEEJqTHKgR-u4vm1Q2fzrvWK0)
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

今回の場合、端末A の remote に fork 元を upstream として加えることで、
端末A 経由で origin に upstream の変更を反映させることができるようになる。

{% capture url %}{{site.github.url}}{% link _docs/Git/images/pull-fork-origin/02_upstream.svg %}{% endcapture %}
{% capture caption %}
[source](http://www.plantuml.com/plantuml/uml/RP2_IWD14CRxxoc6z7U5TY8XWlW2jZPBCZcNxEqUixEDVue8mc4breBfKzZuHiVfQxYOd1wOzllp-vYPHJMYgNQ0MdLCoo2h_h7j7bxwfqstRhlrMx_vw9zR8AwiRuoOcivvQPBJY-3rqjum7Hy1mdCrld9CmdLGfbk0A6eGKp45iPNr8o4rKOLD3Tm3ymoIu6YIceLHllA3Eo4JQIUE5xwsxv-lspDWo55nDzrlqCdF3S2kHcKv_HqR5Sgi3vnSgQnIavp37a9qMXJ5lq2y_Wja_o01C_QB_D5l)
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## upstream の変更を確認し、反映する

{% capture text %}

本家リポジトリの変更を取り出すためには fetch と merge の手順を取ります。

まずfetch

```sh
$ git fetch upstream
remote: Counting objects: 1, done.
remote: Total 1 (delta 0), reused 1 (delta 0)
Unpacking objects: 100% (1/1), done.
From git://github.com/DQNEO/Renshu
 * [new branch]      develop    -> upstream/develop
 * [new branch]      master     -> upstream/master
```

カレントブランチを master にして、upstream/master を merge。

```sh
$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.

$ git merge upstream/master
Updating fe579f8..a1e70ae
Fast-forward
```

変更が無い場合には "Already up-to-date." と表示されて終わりです。今回は差分があったので、コミットIDがfe579f8からa1e70aeまでの差分がmergeされました。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita](https://qiita.com/xtetsuji/items/555a1ef19ed21ee42873 "GitHubでFork/cloneしたリポジトリを本家リポジトリに追従する - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この他、 fork 元のブランチを持ってくることなどもできる。

```sh
git checkout -b branchA upstream/branchA
```