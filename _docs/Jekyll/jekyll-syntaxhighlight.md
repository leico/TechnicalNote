---
layout : post
title  : Jekyllのシンタックスハイライトのテーマを作って変更する
date : 2018/07/05
lastchange : 2018-07-05 19:24:56.
tags   :
  - jekyll
  - github pages
  - syntax
  - highlight
  - rouge
  - pygments
---


## 普段の環境と同じテーマにしたい

と思ったので普段使っているVimのカラースキームからJekyll用のカラースキームをおこしたのの共有と、
その過程を書いておく。

参考:

* [List of tokens · jneen/rouge Wiki](https://github.com/jneen/rouge/wiki/List-of-tokens)
* [pygments-css/vs.css at master · richleland/pygments-css](https://github.com/richleland/pygments-css/blob/master/vs.css)
* [Solarized theme stylesheets for Jekyll and Pygments](https://gist.github.com/nicolashery/5765395)
* [Pygments Syntax Highlighter CSS Theme Files](http://jwarby.github.io/jekyll-pygments-themes/languages/javascript.html)
* [desert.vim/desert.vim at master · fugalh/desert.vim](https://github.com/fugalh/desert.vim/blob/master/colors/desert.vim)
* [Vimでの補完ツールプラグインをneocompleteからdeopleteへ](https://rcmdnk.com/blog/2017/11/16/computer-vim/)
* [カラースキームを作ってみよう - 永遠に未完成](https://thinca.hatenablog.com/entry/20130410/1365530054)
* [rgb.txt](http://fugal.net/vim/rgbtxt.html)


## RougeのCSSクラスはPygmentsのCSSクラスと互換性がありそうだ

Github pages / Jekyll のシンタックスハイライトにはRougeが使われており、
Rougeのシンタックスハイライトで用いられている各クラス名とその割り当てがWikiに一覧になっていた。

* [List of tokens · jneen/rouge Wiki](https://github.com/jneen/rouge/wiki/List-of-tokens)

これとWeb上に転がっているPygments用のCSSを見比べてみたところ、どうやら互換性がありそうだと気づいた。

* [pygments-css/vs.css at master · richleland/pygments-css](https://github.com/richleland/pygments-css/blob/master/vs.css)
* [Solarized theme stylesheets for Jekyll and Pygments](https://gist.github.com/nicolashery/5765395)

さらにいろいろ探していたら、Pygmentsのテーマを作成することができるサイトを見つけた。

* [Pygments Syntax Highlighter CSS Theme Files](http://jwarby.github.io/jekyll-pygments-themes/languages/javascript.html)

これでたたき台を作って、必要な箇所は修正する、という方法でシンタックスハイライトを作ることにした。

## Vimのカラースキームを調査する

* [desert.vim/desert.vim at master · fugalh/desert.vim](https://github.com/fugalh/desert.vim/blob/master/colors/desert.vim)

このカラースキームを解読する。

{% capture text %}
後者の、構文ハイライトに使われるもののリストは、`:help group-name` で確認できる。

| --------------- | --------------------------------------------------- |
| \*Comment       | コメント                                            |
| --------------- | --------------------------------------------------- |
| \*Constant      | 定数                                                |
| String          | 文字列定数: `"これは文字列です"`                    |
| Character       | 文字定数: `'c'`, `'\n'`                             |
| Number          | 数値定数: `234`, `0xff`                             |
| Boolean         | ブール値の定数: `TRUE`, `false`                     |
| Float           | 不動小数点数の定数: `2.3e10`                        |
| --------------- | --------------------------------------------------- |
| \*Identifier    | 変数名                                              |
| Function        | 関数名(クラスメソッドを含む)                        |
| --------------- | --------------------------------------------------- |
| \*Statement     | 命令文                                              |
| Conditional     | `if`, `then`, `else`, `endif`, `switch`, その他     |
| Repeat          | `for`, `do`, `while`, その他                        | 
| Label           | `case`, `default`, その他                           | 
| Operator        | `sizeof`, `+`, `*`, その他                          | 
| Keyword         | その他のキーワード                                  |
| Exception       | `try`, `catch`, `throw`                             |  
| --------------- | --------------------------------------------------- |
| \*PreProc       | 一般的なプリプロセッサー命令                        | 
|  Include        | `#include` プリプロセッサー                         | 
|  Define         | `#define` プリプロセッサー                          | 
|  Macro          | Defineと同値                                        |  
|  PreCondit      | プリプロセッサーの `#if`, `#else`, `#endif`, その他 |
| --------------- | --------------------------------------------------- |
| \*Type          | `int`, `long`, `char`, その他                       |
|  StorageClass   | `static`, `register`, `volatile`, その他            |
|  Structure      | `struct`, `union`, `enum`, その他                   |
|  Typedef        | `typedef` 宣言                                      |
| --------------- | --------------------------------------------------- |
| \*Special       | 特殊なシンボル                                      | 
|  SpecialChar    | 特殊な文字定数                                      |
|  Tag            | この上で `CTRL-]` を使うことができる                |
|  Delimiter      | 注意が必要な文字                                    |
|  SpecialComment | コメント内の特記事項                                |
|  Debug          | デバッグ命令                                        |
| --------------- | --------------------------------------------------- |
| \*Underlined    | 目立つ文章, HTMLリンク                              |
| --------------- | --------------------------------------------------- |
| \*Ignore        | (見た目上)空白, 不可視  `|hl-Ignore|`               |
| --------------- | --------------------------------------------------- |
| \*Error         | エラーなど、なんらかの誤った構造                    |
| --------------- | --------------------------------------------------- |
| \*Todo          |  特別な注意が必要なもの; 大抵はTODO FIXME XXXなど   |


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vimでの補完ツールプラグインをneocompleteからdeopleteへ](https://rcmdnk.com/blog/2017/11/16/computer-vim/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この内容に沿って割り当てられている色をクラス割り当てしていけばよいらしい。

{% capture text %}
#### 最後に

最初にも書きましたが、既存のカラースキームを見るとどんな感じか雰囲気が掴めると思います。
最後に便利サイトを紹介します。Vim の画面から直接色をいじってカラースキームが生成できます。

<http://bytefluent.com/vivify/>

いじれない部品もあったりして網羅的ではないのですが、各部位がなんという名前のハイライトグループか、などが触っててわかるのが良いです。簡単なカラースキームならこれでも作れそうです。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[カラースキームを作ってみよう - 永遠に未完成](https://thinca.hatenablog.com/entry/20130410/1365530054)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ここに紹介されているページで色を拾うのがとても楽だった。

* [rgb.txt](http://fugal.net/vim/rgbtxt.html)

Vimで利用されている名称一覧もここで確認できる。


## 置き換えてできあがったテーマ

* [leico/Desert-css: Colorscheme for Pygments / Rouge](https://github.com/leico/Desert-css)

これをSubmoduleとして読み込み、ハイライトのテーマを変更する。

## サブモジュールとしてページに追加する

```sh
git submodule add https://github.com/leico/Desert-css Submodules/Desert
```

追加した後、 *\_sass* ディレクトリにシンボリックリンクを貼る

```sh
cd _sass
ln -sf ../Submodules/Desert Desert
```

## マスターとなるsassで読み込む

```sass
...
@import "Desert/desert"
...
```

これでカラースキームが有効になる。
