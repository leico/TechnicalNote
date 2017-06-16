---
layout : post
title  : MathJaxをインストールする
date   : 2017/04/01
lastchange : 2017-06-16 22:41:50.
tags   :
  - jekyll
  - github pages
  - submodule
  - mathjax
---

## 数式をWebできれいに表示する

数式を提示する必要が出てきたので数式をきれいに表示する方法をしらべた。結果

* [MathML](https://www.w3.org/Math/)
    * [MathML 日本語情報](http://washitake.com/MathML/)
* [MathJax](https://www.mathjax.org/)
    * [Qiita 上で数式を美しく書けるようになっていた件 (MathJax) - Qiita](http://qiita.com/iizukak/items/04d6e226982bc108bc16)

というものを見つけた。

{% assign text='
## Math MLとの比較

Webページに数式を表示するために[W3C](https://www.w3.org/) が標準化を進めている書式 [MathML](https://www.w3.org/Math/) がある。
よく考えられているのだが[MathML 耐久テスト](https://developer.mozilla.org/ja/docs/Mozilla/MathML_Project/MathML_Torture_Test)参照）、
一番の問題はMathMLとして数式を表すための記述コード量が多くなること、
そしてMathJaxがそうであるようにLaTeX記述でWebページに数式を表示できる

MathJaxを使ってLaTeXのようにして \\(4\sin^2(x)−4\cos^2(x)\\)
を表示するごく単純な表現 `$4∖sin\^2(x)−4∖cos\^2(x)$` はMathMLでは次のように長大になる。
LaTeX表式が数式を美しく簡素に表現できることがわかる。
<math>
  <mn>4</mn>
  <msup>
    <mi>sin</mi>
    <mn>2</mn>
  </msup>
  <mi>x</mi>
  <mo>-</mo>
  <mn>4</mn>
  <msup>
    <mi>cos</mi>
    <mn>2</mn>
  </msup>
  <mi>x</mi>
</math>


```html
<math>
    <mn>4</mn>
    <msup>
      <mi>sin</mi>
      <mn>2</mn>
    </msup>
    <mi>x</mi>
    <mo>-</mo>
    <mn>4</mn>
    <msup>
      <mi>cos</mi>
      <mn>2</mn>
    </msup>
    <mi>x</mi>
</math>
```


' | markdownify %}
{% assign source='
[Webの数式表現](http://www.ic.daito.ac.jp/~mizutani/html/mathexpress.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ということなのでMathJaxを使ってみる。

\\[
y=\log_{e}x
\\]

がきれいに表示されるらしい。

参考

* [Webの数式表現](http://www.ic.daito.ac.jp/~mizutani/html/mathexpress.html)
* [transitive.info - git submodule 使い方](http://transitive.info/article/git/command/submodule/)
* [Git submoduleの抑えておきたい理解ポイントのまとめ - Qiita](http://qiita.com/kinpira/items/3309eb2e5a9a422199e9)
* [Loading and Configuring MathJax — MathJax 2.7 documentation](http://docs.mathjax.org/en/latest/configuration.html#config-files)
* [Using MathJax on Github:Pages](http://www.christopherpoole.net/using-mathjax-on-githubpages.html)

MathJaxの書き方

* [Easy Copy MathJax](http://easy-copy-mathjax.xxxx7.com/)

## `git submodule` でバージョン管理しながらインストール

githubで公開されているリポジトリの場合、`git submodule`を利用すると新しいバージョンがリリースされた時`git checkout BRANCH`で対応できる。
便利なのでsubmoduleでインストールする

```sh
git submodule add -b v2.6-latest https://github.com/mathjax/MathJax.git js/MathJax
```

MathJaxの *v2.6-latest* ブランチを */js/MathJax* ディレクトリにインストールしている。
ローカルでデバッグする際はローカルに *clone* されたものを利用するので、
ダウンロードにしばらくかかる。

```sh
git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

 	new file:   .gitmodules
 	new file:   js/MathJax
```

*.gitmodule* と *js/MathJax* が作られている。

```sh
cd js/MathJax/
git branch
  master
* v2.6-latest
```

*clone* したディレクトリに移動、ブランチを確認してみる。

```sh
cat .gitmodules 
[submodule "js/MathJax"]
	path = js/MathJax
	url = https://github.com/mathjax/MathJax.git
	branch = v2.6-latest
```

*.gitmodule* を表示してみる。


```
cd js/MathJax/
git checkout 2.7-latest
```

更新されたブランチに変更する。
その上でリポジトリを *commit* すると完了。

## MathJaxを呼び出す

必要となる場所より上、通常ページヘッダで

```html
<script type="text/javascript" src="{{site.github.url}}/js/MathJax/MathJax.js?config=TeX-MML-AM_HTMLorMML"></script>
```

と呼び出す。

{% assign text='
To load a configuration file, use `config=filename`
(where filename is one of the names above without the `.js`)
as a parameter to the URL of the `MathJax.js` file. For example

```html
<script type="text/javascript"
   src="https://example.com/MathJax.js?config=TeX-AMS-MML_CHTML">
</script>
```

loads the `config/TeX-AMS-MML_HTMLorMML.js` configuration file 
from the MathJax distributed network service.

' | markdownify %}
{% assign source='
[Loading and Configuring MathJax — MathJax 2.7 documentation](http://docs.mathjax.org/en/latest/configuration.html#config-files)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


とのことで、設定プリセットが *MathJax/config* 以下にいくつか用意されている。
それらを呼び出すには `MathJax.js?config=XXXXX`とするらしい。`XXXXX` はファイル名から `.js`を取ったもの。

> loads the `config/TeX-AMS-MML_HTMLorMML.js` configuration file 

これ間違えてるっぽい。サンプルと内容が違う。

## github pages jekyll との競合を回避しながら書く方法

{% assign text='
> ### Using MathJax on Github:Pages
> Posted on 01, Oct 2012

MathJax is a simple way of including Tex/LaTex/MathML based mathematics in HTML webpages.
To get up and running you need to include the MathJax script in the header of your github pages page,
and then write some maths. For LaTex, there are two delimiters you need to know about,
one for block or displayed mathematics `\[ ... \]` ,
and the other for inline mathematics `\( ... \)` .
Installation

You can find the MathJax documentation here however, 
to get up and running quickly, simply put this into the `<head>` section of the page 
you would like to display mathematics:

```html
<head>
...
    <script type="text/javascript"
            src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
    </script>
...
</head>
```


If you are using jekyll, in _config.yml set

```yaml
markdown: kramdown
```

If it is set to discount for example, 
the markdown parser will automatically replace `x^2` with `x<sup>2</sup>` which interrupts the MathJax rendering.


## Usage

Using it is pretty easy:

```
Here is an example MathJax inline rendering \\( 1/x^{2} \\), and here is a block rendering: 
\\[ \frac{1}{n^{2}} \\]
```


Here is an example MathJax inline rendering \\( 1/x^2 \\)
, and here is a block rendering:
\\[ \frac{1}{n^{2}} \\]

The only thing to look out for is the escaping of the backslash when using markdown,
so the delimiters become `\\[ ... \\]` and `\\( ... \\)` for inline and block maths respectively. 
' | markdownify %}
{% assign source='
[Using MathJax on Github:Pages](http://www.christopherpoole.net/using-mathjax-on-githubpages.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



ということで、

インライン要素で書く
: `\\( ... \\)`

ブロック要素で書く
: `\\[ ... \\]`

上記

> MathJaxを使ってLaTeXのようにして \\(4\sin^2(x)−4\cos^2(x)\\)

の部分は

```latex
\\(4\sin^2(x)−4\cos^2(x)\\)
```

と引用元と記述方法を変更している。

