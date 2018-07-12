---
layout : post
title  : VimRにdeoplete.nvimをインストールする
date   : 2018/07/04
lastchange : 2018-07-09 23:40:44.
tags   :
  - vim
  - VimR
  - dein
  - deoplete
  - python3
  - homebrew
---

## homebrew を入れたしdeopleteを導入する

やっとhomebrewを入れたのでサクッと python3 をインストールしてVimRに補完プラグインのdeopleteを導入する。

参考:
* [Vimでの補完ツールプラグインをneocompleteからdeopleteへ](https://rcmdnk.com/blog/2017/11/16/computer-vim/)
* [Support for :python3 on neovim · Issue #345 · qvacua/vimr](https://github.com/qvacua/vimr/issues/345#issuecomment-260456378)
* [Vim documentation: provider](https://neovim.io/doc/user/provider.html#provider-python)
* [Shougo/neosnippet.vim: neo-snippet plugin](https://github.com/Shougo/neosnippet.vim)
* [neovim で deoplete + neosnippet の連携をする - Qiita](https://qiita.com/takezo/items/809d53ecd37c438bac9e)
* [neovimの補完プラグインdeopleteが重い(快適設定にする) - sinshutu\_kibotuの日記](https://sinshutu-kibotu.hatenablog.jp/entry/2017/01/27/062757)
* [map - Vim日本語ドキュメント](https://vim-jp.org/vimdoc-ja/map.html#:map-%3Cexpr%3E)
* [【図解Vim】mapとnoremap - ここぽんのーと](https://cocopon.me/blog/2013/10/vim-map-noremap/)
* [Vimのmap、noremapについて調べた - 転職した](http://yellowring.hatenablog.jp/entry/2015/03/08/152351)
* [Vim - neocomplete.vimを使った補完後に改行をさせないようにしたい。(116046)\｜teratail](https://teratail.com/questions/116046)
* [neosnippet.vim/neosnippet.txt at master · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/blob/master/doc/neosnippet.txt)





## deopleteの動作環境について

{% capture text %}
## deopleteの導入に必要なこと

ということでdeopleteはVimでも使える様になりましたが、 
条件としてPython 3付きでコンパイルされている必要があります。

`vim --version` で `+python3` となってるか確認。
もしくはVim起動後 `:echo has('python3')` が1になることを確認。

そうでなければソースからビルドするなら `configure` に 
`--enable-python3interp` を渡してビルド。

MacでHomebrewなら

```sh
brew install vim --with-python3
```

勿論Python 3がシステムにインストールされている必要があります。

加えてneovimというPython 3のパッケージがシステムに導入されてる必要があります。 ないとdepleteを起動する時に

```sh
Messages maintainer: Bram Moolenaar <Bram@vim.org>
[vim-hug-neovim-rpc] failed executing: pythonx import neovim
[vim-hug-neovim-rpc] Vim(pythonx):Traceback (most recent call last):
[deoplete] [vim-hug-neovim-rpc] requires `:pythonx import neovim` command to work
[deoplete] function deoplete#enable[1]..deoplete#initialize[1]..deoplete#init#_initialize[11]..deoplete#init#_channel[10]..yarp#core#notify[1]..yarp#core#wai
t_channel[5]..yarp#core#jobstart[2]..yarp#pyx#init[13]..yarp#core#serveraddr[1]..neovim_rpc#serveraddr, line 15
```

みたいなエラーがおきます。 この場合にはpip3で

```sh
$ pip3 install --upgrade neovim
```

の様にインストールしてあげる必要があります。
(neovimパッケージが古い場合にもエラーが出るので
その場合にも同様のコマンドでバージョンアップグレードが必要。)

Neovimでは元々 `python3` が無効な状態でも このneovimパッケージを入れると自動で有効になります
(再コンパイルとか必要なし)。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vimでの補完ツールプラグインをneocompleteからdeopleteへ](https://rcmdnk.com/blog/2017/11/16/computer-vim/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


まずVimRにてPython3が使えるか確認する。




{% capture text %}

Maybe this can help you? <https://neovim.io/doc/user/provider.html#provider-python>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by   [qvacua (Tae Won Ha)](https://github.com/qvacua) 
from [Support for :python3 on neovim · Issue #345 · qvacua/vimr](https://github.com/qvacua/vimr/issues/345#issuecomment-260456378)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

```vim
Python integration		    	      *provider-python*

Nvim supports Python |remote-plugin|s and the Vim legacy |python-vim| and
|python3| interfaces (which are implemented as remote-plugins).
Note: Only the Vim 7.3 API is supported; bindeval (Vim 7.4) is not.

PYTHON QUICKSTART 

If you used a package manager to install Nvim, you might already have the
required "neovim" Python package. Run |:checkhealth| to verify.

To install the package with "pip":

- For Python 2 plugins, make sure Python 2.7 is available in your $PATH, then
  install the "neovim" Python package systemwide:
    sudo pip2 install --upgrade neovim
 
  or for the current user:
    pip2 install --user --upgrade neovim
 
- For Python 3 plugins, make sure Python 3.4+ is available in your $PATH, then
  install the "neovim" Python package systemwide:
    sudo pip3 install --upgrade neovim
 
  or for the current user:
    pip3 install --user --upgrade neovim
 
Note: "pip" may refer to Python 2 or Python 3, so the steps above mention
"pip2" and "pip3" explicitly. If one is missing, try "pip".

Note: The `--upgrade` flag ensures you have the latest version even if
a previous version was already installed.
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vim documentation: provider](https://neovim.io/doc/user/provider.html#provider-python)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


中身がNeoVimなのでNeoVimがPython3に対応しているからオッケー、ということらしい。
それとNeoVimのヘルプのコマンドを実行すればPython3が利用できるようになることがわかった。

現時点では

```vim
:echo has('python3')
0
```

である。

## Python3のインストール

```sh
brew install python3
```

簡単。

## NeoVim用パッケージのインストール

```sh
pip3 install --upgrade neovim
```

こちらも簡単。

ここまでが終了するとVimR上で

```vim
:echo has('python3')
1
```

となる。

## deoplete、neosnippetのインストール

ここまでできたら公式の設定例でひとまずインストールを行う。

`dein.toml`にエントリーを追加する。

```toml
[[plugins]]
repo = 'Shougo/deoplete.nvim'
hook_add = '''
let g:deoplete#enable_at_startup   = 1
'''



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
"imap <expr><TAB>
" \ pumvisible() ? "\<C-n>" :
" \ neosnippet#expandable_or_jumpable() ?
" \    "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"

" smap <expr><TAB> neosnippet#expandable_or_jumpable() ?
" \ "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"

 For conceal markers.
if has('conceal')
  set conceallevel=2 concealcursor=niv
endif
'''
```

VimR上で

```vim
:call dein#install()
```

を実行する。


実行した後に再起動すると、

```
Try the :UpdateRemotePlugins command and restart Neovim. See also :checkhealth.
```

というエラーが表示されたので

```vim
:UpdateRemotePlugins
```

を実行、

```vim
:checkhealth
```

で deoplete が読み込まれているか確認する。


```vim

health#deoplete#check
========================================================================
## deoplete.nvim
  - OK: has("nvim") was successful
  - OK: exists("v:t_list") was successful
  - OK: has("timers") was successful
  - OK: has("python3") was successful
  - INFO: If you're still having problems, try the following commands:
    $ export NVIM_PYTHON_LOG_FILE=/tmp/log
    $ export NVIM_PYTHON_LOG_LEVEL=DEBUG
    $ nvim
    $ cat /tmp/log_{PID}
    and then create an issue on github
```




## deoplete、neosnippetの設定

`dein_lazy.toml`
: 
    ```toml
    # deoplete------------------
    
    [[plugins]]
    repo = 'Shougo/deoplete.nvim'
    hook_add = '''
    let g:deoplete#enable_at_startup   = 1
    let g:deoplete#auto_complete_delay = 0
    inoremap <expr><tab> pumvisible() ? "\<C-n>" :
            \ neosnippet#expandable_or_jumpable() ?
            \    "\<Plug>(neosnippet_expand_or_jump)" : "\<tab>"
    '''
    on_i = 1
    
    
    
    # neosnippet----------------
    
    [[plugins]]
    repo = 'Shougo/neosnippet.vim'
    depends = ['neosnippet-snippets', 'context_filetype.vim']
    on_i = 1
    hook_source = '''
    " Plugin key-mappings.
    " Note: It must be "imap" and "smap".  It uses <Plug> mappings.
    "imap <C-k>     <Plug>(neosnippet_expand_or_jump)
    "smap <C-k>     <Plug>(neosnippet_expand_or_jump)
    "xmap <C-k>     <Plug>(neosnippet_expand_target)
    
    " SuperTab like snippets behavior.
    " Note: It must be "imap" and "smap".  It uses <Plug> mappings.
    imap <expr><TAB>
     \ pumvisible() ? "\<C-n>" :
     \ neosnippet#expandable_or_jumpable() ?
     \    "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"
    
    imap <expr><CR>
    \ (pumvisible() && neosnippet#expandable()) ? "\<Plug>(neosnippet_expand_or_jump)" : "\<CR>"
    
    
    smap <expr><TAB> neosnippet#expandable_or_jumpable() ?
    \ "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"
    
    " For conceal markers.
    "if has('conceal')
    "  set conceallevel=2 concealcursor=niv
    "endif
    '''


    [[plugins]]
    repo = 'Shougo/neosnippet-snippets'
    ```

ひとまずこうなった。

変更点は以下

* `TAB`のほうが使いやすいので SuperTab like を有効化
* deoplete と neosnippet の連携
* 候補を Enter キーで指定して展開
* 候補表示速度を高速に
* 途中記号を省略されると困るソースがあるので conceal 設定を無効化している。

これら各種設定の参考元は以下。


{% capture text %}

## Configuration

This is an example `~/.vimrc` configuration for Neosnippet. 
It is assumed you already have deoplete configured. 
With the settings of the example, you can use the following keys:

* `C-k` to select-and-expand a snippet from the deoplete popup 
    (Use `C-n` and `C-p` to select it). 
    `C-k` can also be used to jump to the next field in the snippet.
* `Tab` to select the next field to fill in the snippet.

```vim
" Plugin key-mappings.
" Note: It must be "imap" and "smap".  It uses <Plug> mappings.
imap <C-k>     <Plug>(neosnippet_expand_or_jump)
smap <C-k>     <Plug>(neosnippet_expand_or_jump)
xmap <C-k>     <Plug>(neosnippet_expand_target)

" SuperTab like snippets behavior.
" Note: It must be "imap" and "smap".  It uses <Plug> mappings.
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
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Shougo/neosnippet.vim: neo-snippet plugin](https://github.com/Shougo/neosnippet.vim)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このように、デフォルトだと`Ctrl + n` で次、`Ctrl + p`で前の候補を選択、`Ctrl + k`で決定となっている。


{% capture text %}


[deoplete](https://github.com/Shougo/deoplete.nvim) 
は [neovim](https://github.com/neovim/neovim) の補完プラグインなのですが，
設定をせずに [neosnippet](https://github.com/Shougo/neosnippet.vim) 
のスニペット機能を同時に利用しようとしてもうまくいきません．
例えば，`for` 文を展開しようとしても，補完結果として `for` と出力されるだけで，
`for` 文のフォーマットをよしなに出力してくれません．
そこで，以下のように，`dein.toml` と `dein_lazy.toml`
に設定するとスニペット機能を利用できます．

`dein.toml`
: 
    ```toml
    # dein.toml
    
    [[plugins]]
    repo = 'Shougo/neosnippet-snippets'
    ```

`dein_lazy.toml`
: 
    ```toml
    # dein_lazy.toml
    
    [[plugins]]
    repo = 'Shougo/deoplete.nvim'
    hook_source = '''
      let g:deoplete#enable_at_startup = 1
      inoremap <expr><tab> pumvisible() ? "\<C-n>" :
            \ neosnippet#expandable_or_jumpable() ?
            \    "\<Plug>(neosnippet_expand_or_jump)" : "\<tab>"
    '''
    on_i = 1
    
    [[plugins]]
    repo = 'Shougo/neosnippet'
    hook_source = '''
      imap <C-k> <Plug>(neosnippet_expand_or_jump)
      smap <C-k> <Plug>(neosnippet_expand_or_jump)
      xmap <C-k> <Plug>(neosnippet_expand_target)
      if has('conceal')
        set conceallevel=2 concealcursor=niv
      endif
    '''
    on_i  = 1
    on_ft = ['snippet']
    depends = ['neosnippet-snippets']
    ```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[neovim で deoplete + neosnippet の連携をする - Qiita](https://qiita.com/takezo/items/809d53ecd37c438bac9e)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

deoplete と neosnippet の連携方法。
neosnippet-snippets を通常読み込みに置いている例が多いけれども、
lazy に置いて depends を記述した状態でひとまず問題なく動いている。




{% capture text %}
# 経緯

deopleteの設定どうするんだろ -> 公式とか導入系の記事を見る -> 大体exampleに

```vim
let g:deoplete#enable_at_startup = 1
```

しか書いてなくてhook_sourceへ設定を突っ込んだりしてみたけど、とにかく入力が重くてなんでだろってなってました。 
他の人の設定ファイルみたりしてたらauto_complete_delayとか他のオプションが設定されていてそれらをいじっていたら良い感じになりました。

快適設定ってタイトルなのに全然最適化みたいなことはしてないです(それ目的な人はすいません)

とりあえず、「deoplete 重い」で記事があまり引っかからなかったので同じ感じでdeoplete導入できてない人が解消できてくれればうれしいです。

---


# deinでの設定
```toml
[[plugins]]
repo = 'Shougo/deoplete.nvim'
on_event = 'InsertCharPre'
hook_source = '''
  let g:deoplete#enable_at_startup = 1
  let g:deoplete#auto_complete_delay = 0
  let g:deoplete#auto_complete_start_length = 1
  let g:deoplete#enable_camel_case = 0
  let g:deoplete#enable_ignore_case = 0
  let g:deoplete#enable_refresh_always = 0
  let g:deoplete#enable_smart_case = 1
  let g:deoplete#file#enable_buffer_path = 1
  let g:deoplete#max_list = 10000
  inoremap <expr><tab> pumvisible() ? "\<C-n>" :
        \ neosnippet#expandable_or_jumpable() ?
        \    "\<Plug>(neosnippet_expand_or_jump)" : "\<tab>"
'''
on_i = 1
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[neovimの補完プラグインdeopleteが重い(快適設定にする) - sinshutu\_kibotuの日記](https://sinshutu-kibotu.hatenablog.jp/entry/2017/01/27/062757)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

候補が表示されるまでなんか時間がかかる気がしたのでこちらを取り入れたら解決した。




{% capture text %}

```vim
:map    {lhs} {rhs}             mapmode-nvo           :map
:nm[ap] {lhs} {rhs}             mapmode-n             :nm :nmap
:vm[ap] {lhs} {rhs}             mapmode-v             :vm :vmap
:xm[ap] {lhs} {rhs}             mapmode-x             :xm :xmap
:smap   {lhs} {rhs}             mapmode-s                 :smap
:om[ap] {lhs} {rhs}             mapmode-o             :om :omap
:map!   {lhs} {rhs}             mapmode-ic            :map!
:im[ap] {lhs} {rhs}             mapmode-i             :im :imap
:lm[ap] {lhs} {rhs}             mapmode-l             :lm :lmap
:cm[ap] {lhs} {rhs}             mapmode-c             :cm :cmap
:tma[p] {lhs} {rhs}             mapmode-t             :tma :tmap
                        キー入力 {lhs} を {rhs} に割り当てます。作成したマップ
                        は、マップコマンドに対応したモードで使用できます。マッ
                        プが使用されたときに {rhs} が調べられ、再マップされま
                        す。マップを入れ子にしたり再帰的にすることができます。

                                                :nore :norem
:no[remap]  {lhs} {rhs}         mapmode-nvo   :no  :noremap :nor
:nn[oremap] {lhs} {rhs}         mapmode-n     :nn  :nnoremap
:vn[oremap] {lhs} {rhs}         mapmode-v     :vn  :vnoremap
:xn[oremap] {lhs} {rhs}         mapmode-x     :xn  :xnoremap
:snor[emap] {lhs} {rhs}         mapmode-s     :snor :snoremap
:ono[remap] {lhs} {rhs}         mapmode-o     :ono :onoremap
:no[remap]! {lhs} {rhs}         mapmode-ic    :no! :noremap!
:ino[remap] {lhs} {rhs}         mapmode-i     :ino :inoremap
:ln[oremap] {lhs} {rhs}         mapmode-l     :ln  :lnoremap
:cno[remap] {lhs} {rhs}         mapmode-c     :cno :cnoremap
:tno[remap] {lhs} {rhs}         mapmode-t     :tno :tnoremap
                        キー入力 {lhs} を {rhs} に割り当てます。作成したマッ
                        プ、はマップコマンドに対応したモードで使用できます。
                        {rhs} は再マップされないので、マップが入れ子になったり
                        再帰的になることはありません。コマンドを再定義するとき
                        によく使われます。
                        {Vi にはありません}


マップや短縮入力を定義するときに `<expr>` 引数を指定すると、引数が式 (スクリプ
ト) として扱われます。マップが実行されたときに、式が評価され、その値が `{rhs}`
として使われます。例:

:inoremap <expr> . InsertDot()
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[map - Vim日本語ドキュメント](https://vim-jp.org/vimdoc-ja/map.html#:map-%3Cexpr%3E)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}








{% capture text %}

# mapとnoremapの違い

「再マップされない」とは、ユーザが定義した他のマップの影響を受けずに、
何も設定されていない「素のVim」が持っている機能に割り当てる、ということだ。

「テメーが `CTRL-A` に何を割り当てたかは知らねーが、素のVimでは `CTRL-A` は『1増やす』だから、
オレは1増やす！」といった具合である。

---

## mapが必要なケース: プラグインが提供する機能を割り当てるとき

ユーザは、例えば「e」キーを押したときに hello world を出力したければ、こんな風に書けばよい。

```vim
:map e <Plug>(say_hello)
```

それで、このときの割り当てをnoremapにしてしまうと問題が発生する。
もし、noremapを使って

```vim
:noremap e <Plug>(say_hello)
```

のように書いてしまうと、noremapは「再マップしない」ので、
「素のVim」のキーマッピングで `<Plug>(say_hello)` に対応するものを探してしまう。 
が、そんなものは定義されているはずもなく、何も実行されない。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【図解Vim】mapとnoremap - ここぽんのーと](https://cocopon.me/blog/2013/10/vim-map-noremap/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





{% capture text %}

#### mapやnoremapの種類

vimにはモードがあるため、mapやnoremapも各々のモード用に種類が存在する。

```vim
      コマンド                    モード
 :map   :noremap  :unmap     ノーマル、ビジュアル、選択、演算待ち状態
 :nmap  :nnoremap :nunmap    ノーマル
 :vmap  :vnoremap :vunmap    ビジュアル、選択
 :smap  :snoremap :sunmap    選択
 :xmap  :xnoremap :xunmap    ビジュアル
 :omap  :onoremap :ounmap    演算待ち状態
 :map!  :noremap! :unmap!    挿入、コマンドライン
 :imap  :inoremap :iunmap    挿入
 :lmap  :lnoremap :lunmap    挿入、コマンドライン、Lang-Arg
 :cmap  :cnoremap :cunmap    コマンドライン
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vimのmap、noremapについて調べた - 転職した](http://yellowring.hatenablog.jp/entry/2015/03/08/152351)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

補完候補の確定を `\<CR>` で行っているということですよね？
`\<CR>` の定義がおかしいのだと思います。デフォルトではそのような挙動はしないはずです。

`verbose inoremap <CR>` で実際の定義を確認したほうが良いでしょう。

```vim
inoremap <expr><CR>  pumvisible() ? neocomplcache#close_popup() : "<CR>"
```

neocomplcache の設定が残っているようです。これは、以下のようにすべきでしょう。

```vim
if neobundle#is_installed('neocomplete.vim')
...
    " バックスペースで補完のポップアップを閉じる
    inoremap <expr><BS> neocomplete#smart_close_popup()."\<C-h>"

    " エンターキーで補完候補の確定. スニペットの展開もエンターキーで確定・・・・・・②
    imap <expr><CR> neosnippet#expandable() ? "\<Plug>(neosnippet_expand_or_jump)" : pumvisible() ? "\<C-y>" : "\<CR>"
    " タブキーで補完候補の選択. スニペット内のジャンプもタブキーでジャンプ・・・・・・③
    imap <expr><TAB> pumvisible() ? "\<C-n>" : neosnippet#jumpable() ? "\<Plug>(neosnippet_expand_or_jump)" : "\<TAB>"
else
  inoremap <expr><CR>  pumvisible() ? neocomplcache#close_popup() : "\<CR>"
endif
```


キーコードが正しく使われていないことが気になります。`\<CR>`のようにエスケープが必要です。
現在の設定でちゃんと動作していますか？

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by   [Shougo](https://teratail.com/users/Shougo#reply)
from [Vim - neocomplete.vimを使った補完後に改行をさせないようにしたい。(116046)\｜teratail](https://teratail.com/questions/116046)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

```vim
					*neosnippet#expandable_or_jumpable()*
neosnippet#expandable_or_jumpable()
		You can use this function with imap <expr>. It checks if
		the cursor text is a snippet trigger or a placeholder. This is
		useful to save key mappings.
		Note: If you don't like to jump to the next placeholder, you must use
		|neosnippet#expandable()| instead of
		|neosnippet#expandable_or_jumpable()|.
>
		imap <expr><C-l>
		\ neosnippet#expandable_or_jumpable() ?
		\ "\<Plug>(neosnippet_expand_or_jump)" : "\<C-n>"
<
```

```vim
" Expand the completed snippet trigger by <CR>.
	"imap <expr><CR>
	"\ (pumvisible() && neosnippet#expandable()) ?
"\ "\<Plug>(neosnippet_expand)" : "\<CR>"
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[neosnippet.vim/neosnippet.txt at master · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/blob/master/doc/neosnippet.txt)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


などを参考にしてこの一文を加えた。

```vim
imap <expr><CR>
\ (pumvisible() && neosnippet#expandable()) ? "\<Plug>(neosnippet_expand_or_jump)" : "\<CR>"
```

