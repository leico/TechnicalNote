---
layout : post
title  : Vim:Vimのディレクトリ構成
date   : 2017/02/04
lastchange : 2017-03-05 17:09:00.
tags   :
  - vim
  - VimR
---

## きっかけ

__autodate.vim__ でファイル毎に異なる設定をしたいと思っていたら、vimの仕様を調べることになった。
下記ページほぼそのままになるが、まとめておく。


参考

* [vimエディタのプラグインファイルの置き場所を変更する](http://nanasi.jp/articles/howto/config/runtimepath.html)
* [Vim プラグインで幸せな生活を送る](http://momota.github.io/blog/2013/08/29/vim-plugins/)
* [vimで読み込まれるファイルについて](http://qiita.com/okamos/items/8279154e20249247f78f)

## __runtimepath__ について

> vimエディタがどのディレクトリから設定ファイル、プラグインファイルを読み込むのかを
> 決定しているのが「runtimepath」オプションです。「runtimepath」オプションの値はディレクトリのリストで、
> vimエディタは起動時、「runtimepath」オプションで指定されたディレクトリから、
> スクリプトファイルを順々に検索し、ロードします。 (Windows, Mac) 
> 
> ---
> 
> * [vimエディタのプラグインファイルの置き場所を変更する](http://nanasi.jp/articles/howto/config/runtimepath.html)

## __runtimepath__ を調べる

> * 通常は $HOME/.vim 。runtimepathは以下のviコマンドで確認できる。
> 
> ```
> :set runtimepath
> ```
> 
> ---
> 
> * [Vim プラグインで幸せな生活を送る](http://momota.github.io/blog/2013/08/29/vim-plugins/)

> ## runtimepathが重要
> 
> > vimのファイル読込においてruntimepathが重要な役割を果たします。
> > VimのDocumentationにはruntimepath内の特定のファイルが順繰り読み込まれることが明記されています。
> > さっそくruntimepath設定を見る
> 
> ```
> :set runtimepath
> ```
> 
> ---
> 
> * [vimで読み込まれるファイルについて](http://qiita.com/okamos/items/8279154e20249247f78f)

`:set runtimepath`をVimRで実行したらこのようになっていた。

* `~/.config/nvim`
* `/etc/xdg/nvim`
* `~/.local/share/nvim/site`
* `/usr/local/share/nvim/site`
* `~/.cache/dein/.cache/init.vim/.dein`
* `/usr/share/nvim/site`
* `/Applications/VimR.app/Contents/Frameworks/SwiftNeoVim.framework/Resources/runtime`
* `/usr/share/nvim/site/after`
* `/usr/local/share/nvim/site/after`
* `~/.local/share/nvim/site/after`
* `~/.config/nvim/after`
* `~/.config/nvim/dein/repos/github.com/Shougo/dein.vim`
* `~/.cache/dein/.cache/init.vim/.dein/after`

たくさん読んでいる・・・。特別な事情がない限り、使うのは`~/.config/nvim`、`~/.config/nvim/after`でしょう。

## __rutimepath__ に配置されているディレクトリの役割

> 検索されるファイルとディレクトリは以下の通りです。
> 
> `filetype.vim`
> : ファイル名によるfiletypeの決定
> 
> `scripts.vim`
> : ファイルの内容によるfiletypeの決定
> 
> `autoload/`
> : 自動的に読み込まれるスクリプト
> 
> `colors/`
> : カラースキーマの定義
> 
> `compiler/`
> : コンパイラ定義ファイル
> 
> `doc/`
> : ヘルプドキュメント
> 
> `ftplugin/`
> : ファイルタイプ別のプラグイン
> 
> `ftdetect/`
> : ファイルタイプを判別するファイル
> 
> `indent/`
> : インデントの設定
> 
> `keymap/`
> : キーマップの定義
> 
> `lang/`
> : メニューの翻訳(GVim)
> 
> `menu.vim`
> : メニューの設定(GVim)
> 
> `plugin/`
> : プラグインスクリプト
> 
> `print/`
> : 印刷用
> 
> `spell/`
> : スペルチェック用
> 
> `syntax/`
> : 文法
> 
> `tutor/`
> : チュートリアル
> 
> ---
> 
> * [vimで読み込まれるファイルについて](http://qiita.com/okamos/items/8279154e20249247f78f)

文章構造がアレだったので少し直して引用。
みんな定義リスト使おう。

## __dein.vim__ の役割

プラグインを大量導入するとこのディレクトリ、ファイル郡の中身が超絶カオスになってゆくので、
プラグイン毎にこれらファイル群を分割管理することで`~/.vim`、`~/.config/nvim`以下を
スッキリきれいに管理することができる。
