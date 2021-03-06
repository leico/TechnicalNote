---
layout : post
title  : VimR+dein.vimで $HOME を使ったパスの設定
date   : 2017/01/25
lastchange : 2018-07-03 17:19:05.
tags   :
  - vim
  - VimR
  - vimrc
  - init.vim
  - env
  - environment
  - variable
  - dein
---

## vimの環境変数

参考

* [Vim script初心者講座 vol.2 ](https://mba-hack.blogspot.jp/2013/11/vim-script-vol2.html#v2m01)
* [文字列型--名無しのvim使い](http://nanasi.jp/articles/code/variable/string.html)
* [starting - Vim日本語ドキュメント](http://vim-jp.org/vimdoc-ja/starting.html)
* [Vimの設定ファイルを作る](http://qiita.com/tetsuya/items/75987487ffac42250d3b)


{% capture text %}
スタート時に、Vimは環境変数とファイルを調べ、然るべく値を設定する。Vimはこの順序で取り掛かる。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[starting - Vim日本語ドキュメント](http://vim-jp.org/vimdoc-ja/starting.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


vim内部にも環境変数がある。以下が設定されている環境変数になる。


{% capture text %}
### Vimで使える環境変数まとめ  

環境変数                                                             | 具体的な使用例                 | 内容              
-------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------------------
[$HOME](http://vim.wikia.com/wiki/Open_vimrc_file)                   | $HOME/.vim/plugin/             | ホームディレクトリを意味します。                                        
[$PATH](http://qiita.com/rbtnn/items/36e86d30c7c00d99050a)           | $PATH/shell.sh                 | よく「パスの通った場所にコピー」等と言われますが、「$PATH」のことを意味します｡「$PATH」の中には、パスに設定した様々なディレクトリが入っています。シェル端末から「$ echo $PATH」で確認して下さい｡このようにすることで､先ほど示されたディレクトリ(パスに設定したディレクトリ)の中に置いたファイルは､ディレクトリを指定しないで､直接ファイルを実行できる(直接実行できるように見せかける)というメリットがあります。  つまり、本来なら「$ /usr/bin/shell.sh」としなければならないところと、「/usr/bin」をパスに設定してるなら、「$ shell.sh」というようにファイル名だけで実行できるようになります｡
[$VIM](http://vim-jp.org/vimdoc-ja/starting.html#$VIM)               | $VIM/runtime                   | Vimが使用するさまざまなファイルの置き場所を見つけるために利用できます。 
[$VIMRUNTIME](http://vim-jp.org/vimdoc-ja/starting.html#$VIMRUNTIME) | $VIMRUNTIME/macros/matchit.vim | ヘルプファイルや構文強調表示の定義ファイルのような、Vimが使用するさまざまな支援ファイルの置き場所を見つけるために使用できます。
[$MYVIMRC](http://vim-jp.org/vimdoc-ja/starting.html#$MYVIMRC)       | :so $MYVIMRC                   | 自分の「.vimrc」という設定ファイルを意味します｡先の具体例は、自分の設定ファイルを読み込むというコマンドです。ちなみに、「:so」は「:source」の省略形です。これは、「ソースを読み込む」という命令であることがほとんどです｡ 
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vim script初心者講座 vol.2 ](https://mba-hack.blogspot.jp/2013/11/vim-script-vol2.html#v2m01)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



これを見ると、`$HOME`がホームディレクトリを指すパスになるようだ。
なのでパスを`$HOME`を使うように変更する。

## vimでの文字連結


{% capture text %}
* 文字列を連結する。

```sh
:echo "AAA" . "BBB"
"# => AAABBB
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[文字列型--名無しのvim使い](http://nanasi.jp/articles/code/variable/string.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



`$HOME . path`で利用することができそうだ。

## `$HOME`を表示してみる

```sh
:echo $HOME
```
で`$HOME`に割り当てられているパスを表示することができる

```sh
/users/user
```

というパスが割り当てられていたので、ホームディレクトリ以降のパスは`/`から始める必要がある。

## __init.vim__ を編集

このようになった

```
"dein Scripts-----------------------------
if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=$HOME/.config/nvim/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state($HOME . '/dotfiles/nvim/dein')

  " dein begin
  call dein#begin($HOME . '/dotfiles/nvim/dein')

  " Let dein manage dein
  " Required:
  call dein#add($HOME . '/dotfiles/nvim/dein/repos/github.com/Shougo/dein.vim')

  " Add or remove your plugins here:
  call dein#add('Shougo/neosnippet.vim')
  call dein#add('Shougo/neosnippet-snippets')

  " Required:
  call dein#end()
  call dein#save_state()
endif

" Required:
filetype plugin indent on
syntax enable

" If you want to install not installed plugins on startup.
"if dein#check_install()
"  call dein#install()
"endif

"End dein Scripts-------------------------
```
