---
layout : post
title  : dein.vim:autodate.vimプラグインを入れる
tags   :
  - vim
  - VimR
  - init.vim
  - dein
  - toml
  - autodate.vim
---

## きっかけ

Jekyllの記事執筆の際に日付を手打ちするのめんどくさいので自動化したいと思った。

参考

* [autodate.vim : ファイルのタイムスタンプ記述を自動的に更新](http://nanasi.jp/articles/vim/autodate_vim.html)
* [Gamburtsev: \[memo\] .vimrc Vundle.vim から Dein.vim へ移行 ](https://zunermuka.blogspot.jp/2016/02/memo-vimrc-vundlevim-deinvim.html)

## __autodate.vim__ プラグインについて

> ファイルの更新時に、その更新時間を自動的にファイルに書き込んでくれるスクリプトです。
> ファイル保存時にファイル内を検索して、特定のキーワードを見つけたらタイムスタンプをセットする仕組みになっています。
> Kaoriya版vimに添付しています。 (Windows, Mac) 
>
> ---
>
> * [autodate.vim : ファイルのタイムスタンプ記述を自動的に更新](http://nanasi.jp/articles/vim/autodate_vim.html)


> #### タイムスタンプ挿入箇所の指定
> 
> 「autodate\_keyword\_pre」、「autodate\_keyword\_post」の2つの設定は、
> タイムスタンプを入れる場所を指定するためのものです。
> 
> autodate.vimスクリプトは、
> "autodate\_keyword\_pre"で設定してキーワードと、
> "autodate\_keyword\_post"で設定してキーワードに挟まれた場所に
> タイムスタンプを挿入します。
> 
> デフォルトでは、
> 「Last Change:.」の間にタイムスタンプを入れる設定になっています。
> 
> | 設定                    | デフォルト値 | 設定例
> | ----------------------- | ------------ | ---
> | autodate\_keyword\_pre 	| Last Change: | :let autodate\_keyword\_pre="[Date:"
> | autodate\_keyword\_post |	.            | :let autodate\_keyword\_post="]"
>
> ---
>
> * [autodate.vim : ファイルのタイムスタンプ記述を自動的に更新](http://nanasi.jp/articles/vim/autodate_vim.html)

## インストール方法

> call dein#add('autodate.vim')
>
> ---
> 
> * [Gamburtsev: \[memo\] .vimrc Vundle.vim から Dein.vim へ移行 ](https://zunermuka.blogspot.jp/2016/02/memo-vimrc-vundlevim-deinvim.html)

というのがあったので、__dein.vim__ のtomlファイルに以下を追加すれば動きそう。

```
[[plugins]]
repo = 'autodate.vim'
```

VimRを起動し

```
:call dein#install()
```

で無事インストールできた。


## 設定


> #### タイムスタンプ挿入箇所の検索範囲の指定
> 
> 「autodate\_lines」は置換キーワードを何行検索するかの指定を指定を行うための設定です。
> 「autodate\_start\_line」で指定した行から「autodate\_lines」で指定した行数の範囲を検索します。
> 
> もし、「autodate\_start\_line」にマイナス値(0より小さい値)が指定された時は、
> ファイル全体を検索するようになります。
> 
> | 設定                  | デフォルト値 | 設定例
> | --------------------- | ------------ | ---
> | autodate\_lines       | 50           | :let autodate\_lines=100
> | autodate\_start\_line | 1            | :let autodate\_start\_line=-1
> 
> #### 挿入するタイムスタンプのフォーマット
> 
> 「autodate\_format」で、autodate.vimスクリプトで
> 挿入されるタイムスタンプのフォーマットを指定することができます。
> 
> | 設定             | デフォルト値 | 設定例
> | ---------------- | ------------ | ---
> | autodate\_format | %d-%3m-%Y    | :let autodate\_format=" %Y/%m/%d %H:%M:%S "
> 
> タイムスタンプのフォーマットの指定例を一通り、下に書いておきます。
> 
> | タイムスタンプのフォーマット | 出力例
> | ---------------------------- | ---
> | %Y/%m/%d                     | 2004/09/27
> | %H:%M:%S                     | 13:06:32
> | %y%m%d-%H%M                  | 040927-1308
> | %d-%3m-%Y                    | 27-Sep-2004
> | %Y/%m/%d %H:%M:%S            | 2004/09/27 13:06:32
> | %c                           | Sun Apr 27 11:49:23 1997
> | %Y %b %d %X                  | 1997 Apr 27 11:53:25
> | %y%m%d %T                    | 970427 11:53:55
> 
> 
> ### その他
> 
> #### autodate.vimスクリプトの機能のオン、オフを切り替える
> 
> 下のコマンドを実行すると、現在開いているバッファのみ、
> autodate.vimスクリプトのタイムスタンプ機能を無効にできます。
> 
> ```
> :AutodateOFF
> ```
> 
> 「:AutodateOFF」コマンドで無効にしたautodate.vimスクリプトの機能を再び有効にするには、
> 下のコマンドを使用します。 現在のバッファのみに有効です。
> 
> ```
> :AutodateON
> ```
> 
> #### 手動でタイムスタンプの挿入を実行する
> 
> 次のコマンドで現在の行に対して、手動でタイムスタンプの挿入ができます。
> コマンドの前に実行の範囲を指定することができす。
> 
> ```
> :Autodate
> :.,.+5Autodate (範囲を指定した時のコマンド実行例)
> ```
> 
> #### アプリケーション起動時にプラグインをロードしないようにする
> 
> もし、このスクリプトをエディタ起動時にロードしたくない場合は、
> 下の設定をvimの設定ファイルに追加してください。
> 
> ```
> :let plugin\_autodate\_disable = 1
> ```
> 
> ---
> 
> * [autodate.vim : ファイルのタイムスタンプ記述を自動的に更新](http://nanasi.jp/articles/vim/autodate_vim.html)
 
