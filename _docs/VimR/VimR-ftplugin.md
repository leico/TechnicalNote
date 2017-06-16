---
layout : post
title  : Vim:ファイル毎にautodate書式を設定する
date   : 2017/02/04
lastchange : 2017-06-17 02:13:24.
tags   :
  - vim
  - VimR
  - dein
  - autodate.vim
---

## きっかけ

__autodate.vim__ は普通では`Last Change:`と`.`の間に日付を入れるが、
Jekyll用にFront-matterに入れてテンプレから呼び出して使いたい。

参考

* [vimスクリプト 「autodate.vim」解説のおまけ : サイト更新停滞ちうっ \[ 名無しのVIM使い \]](http://advweb.seesaa.net/article/694081.html)
* [Vimで現在のバッファ（開いているファイル）のfiletypeを取得する方法](http://vim.g.hatena.ne.jp/tokorom/20110417/1303057714)
* [Front Matter - Jekyll](https://jekyllrb.com/docs/frontmatter/)
* [YAMLの基本について](http://www.task-notes.com/entry/20150922/1442890800)
* [autodate.vim : ファイルのタイムスタンプ記述を自動的に更新--名無しのvim使い](http://nanasi.jp/articles/vim/autodate_vim.html)
* [JekyllのテンプレートのListとHashで結構いけるんじゃないかな](http://qiita.com/daxanya1/items/24695a2a9d8bd6526b53)

## 設定方法


{% capture text %}
#### ファイルタイプごとにタイムスタンプの形式を変える

Windowsの場合
: `$HOME/vimfiles/ftplugin`

Mac OSXの場合
: `$HOME/.vim/ftplugin`

というディレクトリを作って、
そのディレクトリ内にそれぞれのファイルタイプ用の設定ファイルを用意すれば良い。


例えば、

* htmlのファイルタイプの場合は、 __html.vim__
* javaのファイルタイプの場合は、 __java.vim__

を用意して、その中にそれぞれのファイルタイプ用の設定を書きます。
すると、ファイルを開いた時にそれぞれのファイルタイプ用の設定が読み込まれます。


`b:`をつけて、バッファローカルな設定にすると更に良いです。

```
:let b:autodate_keyword_pre = 'Date:'
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[vimスクリプト 「autodate.vim」解説のおまけ : サイト更新停滞ちうっ \[ 名無しのVIM使い \]](http://advweb.seesaa.net/article/694081.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



__ftplugin__ ディレクトリにファイルタイプ毎の設定ファイルを記述すればいいようだ。

## __ftplugin__ ディレクトリの場所

* [Vimのディレクトリ構成]({{site.github.url}}{% link _docs/VimR/Vim-structure.md %})

から定石だと`~/.config/nvim/ftplugin`になるが、せっかく __dein.vim__ でパッケージ管理しているので、
__autodate.vim__ 用に __ftplugin__ を分離したい。

よし。githubでforkして __ftplugin__ を加えたものを作ろうという考えに至った。

## __autodate.vim__ をforkして`git clone`する

もっとも、プラグイン自体のライセンスがよくわからない。
従って苦情がきたらこの方法は使えない。

まずはgithub上で[__autodate.vim__](https://github.com/vim-scripts/autodate.vim)をforkする。

適当なディレクトリで

```
git clone https://github.com/yourgithub/autodate.vim
```

し、中に __ftplugin__ を作って編集する。

まず`.md`ファイルがどんなファイルタイプとして読み込まれているか確認する。



{% capture text %}
#### じつは &filetype だけだった

恥ずかしい話、取得できそうなのにできないな...と思っていたら、

```
&filetype
```

とするだけだった。

例えば、hoge.py を開いているときに

```
:echo &filetype
```

とすれば

```
python
```

と出力される。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vimで現在のバッファ（開いているファイル）のfiletypeを取得する方法](http://vim.g.hatena.ne.jp/tokorom/20110417/1303057714)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



`.md`ファイルを開いている状態で`:echo &filetype`をしてみると

```
markdown
```

と表示された。

なので`autodate.vim/ftplugin/markdown.vim`に変更内容を記述する。



{% capture text %}
| `date` | A date here overrides the date from the name of the post. This can be used to ensure correct sorting of posts. A date is specified in the format `YYYY-MM-DD HH:MM:SS +/-TTTT`; hours, minutes, seconds, and timezone offset are optional. 
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Front Matter - Jekyll](https://jekyllrb.com/docs/frontmatter/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}
「autodate_format」で、autodate.vimスクリプトで挿入されるタイムスタンプのフォーマットを 指定することができます。

| 設定            | デフォルト値 | 設定例
| --------------- | ------------ | --------
| autodate_format | %d-%3m-%Y    | :let autodate_format=" %Y/%m/%d %H:%M:%S "

タイムスタンプのフォーマットの指定例を一通り、下に書いておきます。

| タイムスタンプのフォーマット | 出力例
| ---------------------------- | -----
| %Y/%m/%d                     | 2004/09/27
| %H:%M:%S                     | 13:06:32
| %y%m%d-%H%M                  | 040927-1308
| %d-%3m-%Y                    | 27-Sep-2004
| %Y/%m/%d %H:%M:%S            | 2004/09/27 13:06:32
| %c                           | Sun Apr 27 11:49:23 1997
| %Y %b %d %X                  | 1997 Apr 27 11:53:25
| %y%m%d %T                    | 970427 11:53:55


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[autodate.vim : ファイルのタイムスタンプ記述を自動的に更新--名無しのvim使い](http://nanasi.jp/articles/vim/autodate_vim.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



Jekyllの`date`にフォーマット合わせるべきかなと思うので、

```
let b:autodate_format = "%Y-%m-%d %H:%M:%S"
```

としましょう。

{% capture text %}
### ハッシュ形式

ハッシュはキー`:[半角スペース]値`の形式で表します。コロンのあとに必ず半角スペースを 1 つ以上入れてください。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[YAMLの基本について](http://www.task-notes.com/entry/20150922/1442890800)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



最初詰めて記述したらFront Matter部分でJekyllがエラーを吐いた。YAMLはスペースでハッシュと値に分けている。
なのでprepend文字列にスペースを入れるようにした。

```
let b:autodate_keyword_pre  ='lastchange : '
```

`autodate.vim/ftplugin/markdown.vim`はこうなった。

```
let b:autodate_keyword_pre  ='lastchange : '
let b:autodate_keyword_post ='.'

let b:autodate_format = "%Y-%m-%d %H:%M:%S"
```

## 変更をgithubにpushする

変更した __autodate.vim__ をgithubにpushする。

```
git add .
git commit
git push
```

## tomlファイルの変更

__dein.vim__ のtomlを変更する

```
[[plugins]]
repo = 'yourgithub/autodate.vim'
```

## VimRで読み込む

カスタマイズしたプラグインを読み込む。

```
call dein#install()
```

インストール後、プラグインの内容を変更した場合は改めてgithubへpushし、

```
call dein#update()
```

を行う。

## 変更日時を読み込む



{% capture text %}
{% raw %}
## Front-matterにListとHash構造を定義する

まず、Front-matter及びbody部をこのように定義してみます。

```
---
title: template1
layout: default
hash1:
  name: Hash構造
  value: 値が入る
list2: 
  - 一番目
  - 二番目
---
{{ page.hash1.name }}<br />
{{ page.hash1.value }}<br />
<br />
{% for data in page.list2 }}
{{ data }}<br />
{% endfor %}
```

結果はこうなります。

```
Hash構造
値が入る

一番目
二番目
```

ポイントは、記述した定義は全て"page"というhashに格納されるということです。
{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[JekyllのテンプレートのListとHashで結構いけるんじゃないかな](http://qiita.com/daxanya1/items/24695a2a9d8bd6526b53)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





Front Matterで

```
lastchange : .
```

と書いておくと自動的に最終変更日がセーブ毎に挿入される。

挿入された変更日は

{% raw %}
```
{{ page.lastchange }}
```
{% endraw %}
でページに挿入される。


実際にやってみるとこうなる。

{{ page.lastchange }}
