---
layout : post
title  : dein.vimのキャッシュディレクトリの設定
tags   :
  - vim
  - VimR
  - vimrc
  - init.vim
  - dein
  - XDG
---


## __dein.vim__ のキャッシュディレクトリについて

[__dein.vim__](https://github.com/Shougo/dein.vim) は通常、
インストールしたディレクトリ直下にキャッシュディレクトリを作成し、一時ファイルを保管する。
`~/.cache`に __dein.vim__ をインストールしているなら問題ないが、`~/.config` にインストールしていると嫌な感じだ。

せっかくNeoVimが[XDG base directory specification](http://kledgeb.blogspot.jp/2013/04/ubuntu-10-xdg-base-directory.html)
に対応しているのにもったいない。

のでキャッシュディレクトリを`~/.cache/dein`に移動させる。

> ```
> g:dein#cache_directory
>     The cache directory to use.
> 
>     The default is under the base directory you have already specified by |dein#begin()|.
> ```
> 
> ---
> 
> * [dein.vim/dein.txt at master - Shougo/dein.vim](https://github.com/Shougo/dein.vim/blob/master/doc/dein.txt)

ということなので、`g:dein#cache_directory`を設定すればキャッシュディレクトリが変更される。

## __init.vim__ の変更

```sh
"dein Scripts-----------------------------
if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=$HOME/.config/nvim/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state($HOME . '/dotfiles/nvim/dein')

  " XDG base direcory compartible
  let g:dein#cache_directory = $HOME . '/.cache/dein'

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

途中に

```
" XDG base direcory compartible
let g:dein#cache_directory = $HOME . '/.cache/dein'
```

を追加している。

