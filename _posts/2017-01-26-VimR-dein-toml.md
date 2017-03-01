---
layout : post
title  : dein.vim:tomlファイルでプラグイン管理する
tags   :
  - vim
  - VimR
  - vimrc
  - init.vim
  - dein
  - toml
---

## 最終的なファイル構成と内容

### __~/.config/nvim/init.vim__

```vim
"dein Scripts-----------------------------
if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=$HOME/.config/nvim/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state($HOME . '/.config/nvim/dein')

  " XDG base direcory compartible
  let g:dein#cache_directory = $HOME . '/.cache/dein'

  " dein begin
  call dein#begin($HOME . '/.config/nvim/dein')

 " プラグインリストを収めた TOML ファイル
 " 予め TOML ファイル（後述）を用意しておく
 let s:toml_dir  = $HOME . '/.config/nvim/dein/toml' 
 let s:toml      = s:toml_dir . '/dein.toml'
 let s:lazy_toml = s:toml_dir . '/dein_lazy.toml'

 " TOML を読み込み、キャッシュしておく
 call dein#load_toml(s:toml,      {'lazy': 0})
 call dein#load_toml(s:lazy_toml, {'lazy': 1})


  " Required:
  call dein#end()
  call dein#save_state()
endif

" Required:
filetype plugin indent on
syntax enable

" If you want to install not installed plugins on startup.
#if dein#check_install()
#  call dein#install()
#endif

"End dein Scripts-------------------------


set expandtab
set tabstop=2
set shiftwidth=2
set softtabstop=2
set autoindent
set smartindent
set number

set guifont=Ricty:h14

set showtabline=2

set termguicolors

colorscheme desert

set transparency=15


```

### __~/.config/nvim/dein/toml/dein.toml__

```toml

# Required:
[[plugins]]
repo = 'Shougo/dein.vim'


```

### __~/.config/nvim/dein/toml/dein\_lazy.toml__

```toml



# neosnippet----------------

[[plugins]]
repo = 'Shougo/neosnippet.vim'
depends = ['neosnippet-snippets', 'context_filetype.vim']
on_i = 1
hook_source = '''
" Plugin key-mappings.
" Note: It must be "imap" and "smap".  It uses <Plug> mappings.
imap <C-k>     <Plug>(neosnippet_expand_or_jump)
smap <C-k>     <Plug>(neosnippet_expand_or_jump)
xmap <C-k>     <Plug>(neosnippet_expand_target)

" SuperTab like snippets behavior.
" Note: It must be "imap" and "smap".  It uses <Plug> mappings.
imap <C-k>     <Plug>(neosnippet_expand_or_jump)
"imap <expr><TAB>
" \ pumvisible() ? "\<C-n>" :
" \ neosnippet#expandable_or_jumpable() ?
" \    "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"
smap <expr><TAB> neosnippet#expandable_or_jumpable() ?
\ "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"

" For conceal markers.
if has('conceal')
  set conceallevel=2 concealcursor=niv
endif
'''

[[plugins]]
repo = 'Shougo/neosnippet-snippets'


[[plugins]]
repo = 'Shougo/context_filetype.vim'
```

## きっかけ

プラグインを入れるとプラグインの設定も書かないといけなくなり、__init.vim__ が煩雑になってくるので、
プラグインの設定も含めてtomlファイルに以降することにした。

参考

* [dein.vimによるプラグイン管理のマイベストプラクティス](http://qiita.com/kawaz/items/ee725f6214f91337b42b)
* [NeoBundle から dein.vim に乗り換えたら爆速だった話](http://qiita.com/delphinus/items/00ff2c0ba972c6e41542)
* [\[dein.vim\] hook の便利な使い方](http://qiita.com/delphinus/items/cd221a450fd23506e81a)
* [neovimをインストールしてプラグイン管理をdein.tomlでやってみた](http://qiita.com/ryo2851/items/4e3c287d5a0005780034)
* [NeoVim、そしてdein.vimへ](http://qiita.com/okamos/items/2259d5c770d51b88d75b)
* [shougo-s-github/deinlazy.toml](https://github.com/Shougo/shougo-s-github/blob/master/vim/rc/deinlazy.toml)
* [autocmd - Vim日本語ドキュメント](http://vim-jp.org/vimdoc-ja/autocmd.html)

## __init.vim__ の設定

> ```
> " 設定開始
> if dein#load_state(s:dein_dir)
>   call dein#begin(s:dein_dir)
> 
>   " プラグインリストを収めた TOML ファイル
>   " 予め TOML ファイル（後述）を用意しておく
>   let g:rc_dir    = expand('~/.vim/rc')
>   let s:toml      = g:rc_dir . '/dein.toml'
>   let s:lazy_toml = g:rc_dir . '/dein_lazy.toml'
> 
>   " TOML を読み込み、キャッシュしておく
>   call dein#load_toml(s:toml,      {'lazy': 0})
>   call dein#load_toml(s:lazy_toml, {'lazy': 1})
> 
>   " 設定終了
>   call dein#end()
>   call dein#save_state()
> endif
> 
> " もし、未インストールものものがあったらインストール
> if dein#check_install()
>   call dein#install()
> endif
> ```
> 
> ---
> 
> * [NeoBundle から dein.vim に乗り換えたら爆速だった話](http://qiita.com/delphinus/items/00ff2c0ba972c6e41542)

tomlを読み込む記述をする必要があるので、上記から

```
 " プラグインリストを収めた TOML ファイル
 " 予め TOML ファイル（後述）を用意しておく
 let g:rc_dir    = expand('~/.vim/rc')
 let s:toml      = g:rc_dir . '/dein.toml'
 let s:lazy_toml = g:rc_dir . '/dein_lazy.toml'

 " TOML を読み込み、キャッシュしておく
 call dein#load_toml(s:toml,      {'lazy': 0})
 call dein#load_toml(s:lazy_toml, {'lazy': 1})
```

の部分を変更して __init.vim__ に追加する

```

if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=$HOME/.config/nvim/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state($HOME . '/.config/nvim/dein')

  " XDG base direcory compartible
  let g:dein#cache_directory = $HOME . '/.cache/dein'

  " dein begin
  call dein#begin($HOME . '/.config/nvim/dein')

 " プラグインリストを収めた TOML ファイル
 " 予め TOML ファイル（後述）を用意しておく
 let s:toml_dir  = $HOME . '/.config/nvim/dein/toml' 
 let s:toml      = s:toml_dir . '/dein.toml'
 let s:lazy_toml = s:toml_dir . '/dein_lazy.toml'

 " TOML を読み込み、キャッシュしておく
 call dein#load_toml(s:toml,      {'lazy': 0})
 call dein#load_toml(s:lazy_toml, {'lazy': 1})


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

```

tomlファイルの所在は`~/.config/nvim/dein/toml`ディレクトリにした。
tomlファイルにプラグイン類を記述する。

## tomlファイルの書き方

> ### dein.toml
> 
> さきほどのinit.vim内にプラグインの情報はdein.tomlから読み取るといったことが書かれています。
> 別ファイルでプラグインを管理できるのでinit.vimがすっきりして良いですね。
> deopleteを導入するためのdein.tomlを以下に記載します。
> 
> __~/.config/nvim/dein.toml__
> 
> ```
> [[plugins]]
> repo = 'Shougo/deoplete.nvim'
> hook_add = 'let g:deoplete#enable_at_startup = 1'
> ```
> 
> ---
> 
> * [neovimをインストールしてプラグイン管理をdein.tomlでやってみた](http://qiita.com/ryo2851/items/4e3c287d5a0005780034)

> ```
> # 基本は github.com のレポジトリーを指定するだけ
> [[plugins]]
> repo = 'Shougo/dein.vim'
> ```
> 
> ---
> 
> * [dein.vimによるプラグイン管理のマイベストプラクティス](http://qiita.com/kawaz/items/ee725f6214f91337b42b)


ということでひとまず __dein.toml__ をこのようにした

```
# Required:
[[plugins]]
repo = 'Shougo/dein.vim'

# neosnippet----------------
[[plugins]]
repo = 'Shougo/neosnippet.vim'

[[plugins]]
repo = 'Shougo/neosnippet-snippets'
```

VimRを再起動して動作することを確認する。

## プラグインの遅延読み込み

```
call dein#load_toml(s:lazy_toml, {'lazy': 1})
```

__init.vim__ でなにやら`lazy`というフラグを設定している部分がある。


> - 特定の条件で後から読み込むもの
>   - `~/.vim/rc/dein_lazy.toml`
> 
> ---
> 
> * [NeoBundle から dein.vim に乗り換えたら爆速だった話](http://qiita.com/delphinus/items/00ff2c0ba972c6e41542)

`{'lazy':1}`をセットしておくと遅延読み込みになるようだ。


## プラグインの挙動を制御する

> ### 1. dein.toml の例
> 
> NeoBundle & dein.vim を初めて触る方は 1. だけで十分です。
> 
> > // 追記ここから 2016/04/09
> > 
> > build パラメータの書式が変わりましたので vimproc の設定方法を修正しました。くわしくはこちらを参照してください。
> > 
> > // 追記ここまで
> 
> ```
> # git clone 後、実行すべきコマンドがある場合はこんな感じ
> [[plugins]]
> repo = 'Shougo/vimproc.vim'
> hook_post_update = '''
>   if dein#util#_is_windows()
>     let cmd = 'tools\\update-dll-mingw'
>   elseif dein#util#_is_cygwin()
>     let cmd = 'make -f make_cygwin.mak'
>   elseif executable('gmake')
>     let cmd = 'gmake'
>   else
>     let cmd = 'make'
>   endif
>   let g:dein#plugin.build = cmd
> '''
> 
> # ブランチやタグを指定したいとき
> [[plugins]]
> repo = 'delphinus35/typescript-vim'
> rev  = 'colorize-template-strings'
> 
> # 特定の条件で読み込みたいとき
> [[plugins]]
> repo = 'elzr/vim-json'
> if   = '''! has('kaoriya')'''
> 
> # 依存関係を指定したいとき
> [[plugins]]
> repo    = 'vim-airline/vim-airline'
> depends = ['vim-airline-themes']
> 
> # 依存関係を指定したからと言って、自動でインストールはされない（ここは NeoBundle と違う）
> [[plugins]]
> repo = 'vim-airline/vim-airline-themes'
> ```
> 
> ### 2. dein_lazy.toml の例
> 
> プラグインの数が多くなってくると起動速度が遅くなってきます。必ずしも起動時に必要でないプラグインは、利用条件と共に 2. に移しましょう。
> 
> ```
> # 特定のファイルタイプで読み込む
> [[plugins]]
> repo  = 'Quramy/tsuquyomi'
> on_ft = ['typescript']
> 
> # インサートモードに入ったら読み込む
> [[plugins]]
> repo = 'Shougo/neocomplete.vim'
> on_i = 1
> 
> [[plugins]]
> repo   = 'Shougo/unite.vim'
> 
> # unite.vim を読み込んだら一緒に読み込む
> [[plugins]]
> repo      = 'Shougo/neomru.vim'
> on_source = ['unite.vim']
> 
> # 特定のコマンドを打ったら読み込む
> [[plugins]]
> repo      = 'thinca/vim-prettyprint'
> on_cmd    = ['PP', 'PrettyPrint']
> 
> # 特定のマッピングを使ったら読み込む
> # 又、gvim でしか利用しない
> [[plugins]]
> repo   = 'thinca/vim-fontzoom'
> on_cmd = ['Fontzoom']
> on_map = ['<Plug>(fontzoom-']
> gui    = 1
> ```
> 
> などなど、他にもありますので `:h dein` してマニュアルを読んでみてください。
> 
> ---
> 
> * [dein.vimによるプラグイン管理のマイベストプラクティス](http://qiita.com/kawaz/items/ee725f6214f91337b42b)


> `on_i` 
> : 1を指定すると、インサートモードに入った時に読み込まれます
> 
> `on_ft`
> : 指定したファイルタイプの時に読み込まれます
> 
> `on_path` 
> : バッファ名が一致した時に読み込まれます。`.*`を指定すると何かのファイルを開いた時に読み込まれるので、filerなどのプラグインで使うと便利です
> 
> `on_source`
> : 記載されたプラグインが読み込まれた後に読み込まれます
> 
> `on_cmd`
> : コマンドが実行された時に読み込まれます。`['Unite', 'UniteResume']`のようなプラグインのコマンドを指定するかと思います
> 
> `on_map`
> : `on_cmd`のマッピング版で`['<Plug>(neosnippet_expand_or_jump)']`のように指定します
> 
> `depends`
> : プラグイン間で依存性がある場合に使います。
> __ここに記述しても自動的にインストールされるわけじゃない点はneobundle.vimとは少し挙動が違うので要注意の人もいるかもしれません__
> 
> `if`
> : `has("nvim")` などのように書いて条件に一致した場合のみ読み込まれます
> 
> ---
> 
> * [NeoVim、そしてdein.vimへ](http://qiita.com/okamos/items/2259d5c770d51b88d75b)

## 色んな場面での前処理:hook

> # hook の種類
> 
> | hook の名前        | 実行されるタイミング                         | lazy OFF | lazy ON |
> |--------------------|----------------------------------------------|----------|---------|
> | `hook_add`         | プラグインが dein.vim によって追加されたとき | OK       | OK      |
> | `hook_source`      | プラグインが読み込まれる直前                 | NG       | OK      |
> | `hook_post_source` | プラグインが読み込まれた直後                 | NG       | OK      |
> | `hook_post_update` | プラグインが更新された直後                   | OK       | OK      |
> 
> 以下、それぞれについて例を挙げて見ていきます。
> 
> ## `hook_add`
> 
> dein.vim によってプラグインが認識されたときに発火される hook です。non lazy な（Vim の起動と同時に読み込まれる）プラグインの場合、設定情報を置くにはこれを使うしかありません。
> 
> 便利な用途としては、プラグインを起動するための mapping を書いたり、プラグインのための変数を設定したりします。
> 
> ```toml
> [[plugins]]
> repo     = 'tpope/vim-capslock'
> hook_add = 'imap <C-L> <Plug>CapsLockToggle'
> 
> [[plugins]]
> repo     = 'elzr/vim-json'
> hook_add = 'let g:vim_json_syntax_conceal = 0'
> ```
> 
> ## `hook_source`
> 
> プラグインを読み込む直前に実行されます。たとえばこんな感じ。
> 
> ```toml
> [[plugins]]
> repo        = 'Shougo/echodoc.vim'
> hook_source = 'let g:echodoc_enable_at_startup = 1'
> ```
> 
> この程度の分量なら `hook_add` と大して違いはない（あるいは `vimrc` に直接書くのと変わらない）ですが、本当に必要になるときまで設定を遅らせることができるので、Vim 起動時に環境を汚さないようになります。
> 
> ## `hook_post_source`
> 
> プラグインを読み込んだ直後に実行されます。`hook_source` と似てますが、プラグイン自体の機能が必要なときはこちらに設定しなければなりません。
> 
> ```toml
> [[plugins]]
> repo    = 'osyo-manga/vim-watchdogs'
> depends = [
>   'vim-hier',
>   'quickfixstatus',
>   'shabadou.vim',
>   'vim-quickrun',
> ]
> # 追記参照 2016/05/09
> on_event = ['FocusLost', 'CursorHold']
> hook_add = '''
>   let g:quickrun_config = get(g:, 'quickrun_config', {})
>   let g:quickrun_config['watchdogs_checker/_'] = {'outputter/quickfix/open_cmd': ''}
>   let g:quickrun_config['watchdogs_checker/jshint'] = {'cmdopt': '--config `/.jshintrc'}
> '''
> hook_post_source = '''
>   call watchdogs#setup(g:quickrun_config)
> '''
> ```
> 
> [vim-watchdogs][] はファイル保存時などに非同期でコンパイルチェックをしてくれるプラグインです。依存関係の複雑なプラグインですので、`on_event` を使って Vim 起動後、非同期に読み込んでいます。
> 
> [vim-watchdogs]: https://github.com/osyo-manga/vim-watchdogs
> 
> > /* 追記ここから 2016/05/09 */
> > 
> > `on_idle` は deprecated になりました。[該当部分のコード][on_idle]を見るとわかりますように、現在は単なる `on_event` へのエイリアスになっています。
> > 
> > [on_idle]: https://github.com/Shougo/dein.vim/blob/c441cf8c22521ab220917e88c14dade56bf82859/autoload/dein/parse.vim#L155-L157
> > 
> > /* 追記ここまで */
> 
> このプラグインは設定の最後に `watchdogs#setup()` を呼ぶ必要があるのですが、これは当然、プラグインを読み込んだ後でないと実行できません。こういうときに `hook_post_source` は便利です。
> 
> ## `hook_post_update`
> 
> [vimproc.vim][] や [Tern for Vim][] 等、インストール後にコンパイルや関連ツールのインストールが必要なプラグインがあります。dein.vim では、このようなとき `build` オプションが使えます。
> 
> [vimproc.vim]: https://github.com/Shougo/vimproc.vim
> [Tern for Vim]: https://github.com/ternjs/tern_for_vim
> 
> ```toml
> [[plugins]]
> repo  = 'Shougo/vimproc.vim'
> build = 'make'
> ```
> 
> 以前までこのオプションには OS 毎に別のコマンドを指定することが出来ましたがその機能がなくなってしまいました（[このコメント][]で教えていただきました。thx!）。現在はこのような用途で `hook_post_update` を使うことが[推奨されています][]。
> 
> [このコメント]: http://qiita.com/delphinus35/items/00ff2c0ba972c6e41542#comment-97471a8d81044e70346a
> [推奨されています]: https://twitter.com/ShougoMatsu/status/716111235918331904
> 
> ```toml
> [[plugins]]
> repo = 'Shougo/vimproc.vim'
> hook_post_update = '''
>   if dein#util#_is_windows()
>     let cmd = 'tools\\update-dll-mingw'
>   elseif dein#util#_is_cygwin()
>     let cmd = 'make -f make_cygwin.mak'
>   elseif executable('gmake')
>     let cmd = 'gmake'
>   else
>     let cmd = 'make'
>   endif
>   let g:dein#plugin.build = cmd
> '''
> ```
> 
> 別にこの `hook_post_update` に限った話ではないのですが、全ての `hook` では `g:dein#plugin` を直接指定して設定を動的に書き換えることができます。
> 
> ---
>
> * [\[dein.vim\] hook の便利な使い方](http://qiita.com/delphinus/items/cd221a450fd23506e81a)


### Shougo氏の __neosnippet.vim__ に対する設定を見てみる

> ```
> [[plugins]]
> repo = 'Shougo/neosnippet.vim'
> depends = ['neosnippet-snippets', 'context_filetype.vim']
> on_event = 'InsertCharPre'
> on_ft = 'snippet'
> hook_source = '''
>   imap <silent>L     <Plug>(neosnippet_jump_or_expand)
>   smap <silent>L     <Plug>(neosnippet_jump_or_expand)
>   xmap <silent>L     <Plug>(neosnippet_expand_target)
>   let g:neosnippet#enable_snipmate_compatibility = 1
>   let g:neosnippet#enable_completed_snippet = 1
>   let g:neosnippet#expand_word_boundary = 1
>   " let g:snippets_dir = '~/.vim/snippets/,~/.vim/bundle/snipmate/snippets/'
>   let g:neosnippet#snippets_directory = '~/.vim/snippets'
> '''
> ```
> 
> ---
> 
> * [shougo-s-github/deinlazy.toml](https://github.com/Shougo/shougo-s-github/blob/master/vim/rc/deinlazy.toml)

`depends = ['neosnippet-snippets', 'context_filetype.vim']`
: __neosnippet-snippets__ 、 __context_filetype.vim__ の2つを依存関係に指定しているのでこれらが先に読み込まれる


`on_event = 'InsertCharPre'`
: > ``` 
  > |InsertCharPre|         挿入モードで文字が入力されたとき、その文字が挿入される前
  > ```
  >
  > ---
  > 
  > * [autocmd - Vim日本語ドキュメント](http://vim-jp.org/vimdoc-ja/autocmd.html)


  なので、文字が挿入される前に読み込む設定になっている。

`on_ft = 'snippet'`
: filetypeがsnippetの時に読み込まれる

`hook_source`
: プラグインを読み込む直前に実行される処理、つまり __init.vim__ に書くプラグインの設定内容になる。

というのが遅延読み込み用のtomlファイルに記述されている。
これを参考に __dein_lazy.toml__ に __neosnippet.vim__ の設定を記述してみる。

```
# neosnippet----------------

[[plugins]]
repo = 'Shougo/neosnippet.vim'
depends = ['neosnippet-snippets', 'context_filetype.vim']
on_event = 'InsertCharPre'
on_ft = 'snippet'
hool_source = '''
# Plugin key-mappings.
# Note: It must be "imap" and "smap".  It uses <Plug> mappings.
imap <C-k>     <Plug>(neosnippet_expand_or_jump)
smap <C-k>     <Plug>(neosnippet_expand_or_jump)
xmap <C-k>     <Plug>(neosnippet_expand_target)

# SuperTab like snippets behavior.
# Note: It must be "imap" and "smap".  It uses <Plug> mappings.
imap <C-k>     <Plug>(neosnippet_expand_or_jump)
#imap <expr><TAB>
# \ pumvisible() ? "\<C-n>" :
# \ neosnippet#expandable_or_jumpable() ?
# \    "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"
smap <expr><TAB> neosnippet#expandable_or_jumpable() ?
\ "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"

# For conceal markers.
if has('conceal')
  set conceallevel=2 concealcursor=niv
endif
'''

[[plugins]]
repo = 'Shougo/neosnippet-snippets'


[[plugins]]
repo = 'Shougo/context_filetype.vim'
```

さて、実行されるか。再起動して試す。

__neosnippet-snippets__ 、 __context_filetype.vim__ などインストールされていなければ

```
:call dein#install()
```

をVimR上で実行する。
