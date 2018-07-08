---
layout : post
title  : neosnippet用 C/C++11 スニペットを作成する。
date   : 2018/07/08
lastchange : 2018-07-08 17:27:52.
tags   :
  - vim
  - VimR
  - deoplete
  - neosnippet
  - snip
  - C++11
  - C
---

## テンプレートメタとかに対応したスニペットを作ろう

と思ったので作成した。その時に参考にしたページとかをまとめる。

作成したスニペットはこちら

[leico/neosnippet-cpp: Neosnippet for C/C++](https://github.com/leico/neosnippet-cpp)

参考:

* [Vimのneosnippetで自分用snippetファイルを追加したい - 300億円欲しい](http://gg-hogehoge.hatenablog.com/entry/2014/04/05/230043)
* [neosnippet.vim/neosnippet.txt at master · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/blob/master/doc/neosnippet.txt)
* [dein.vim:tomlファイルでプラグイン管理する]({{site.github.url}}{% link _docs/VimR/VimR-dein-toml.md %})
* [Ignoring/overriding specified snippets · Issue #429 · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/issues/429)
* [NeoSnippetのsnippetファイルの設定 - adragoonaの日記](http://d.hatena.ne.jp/adragoona/20130929/1380437722)


## githubの自作スニペットを読み込むための設定

{% capture text %}
### 自分用スニペット

自分でスニペットを定義して読み込ませたいです.

vimrcに

```vim
" 自分用 snippet ファイルの場所
let s:my_snippet = '~/snippet/'
let g:neosnippet#snippets_directory = s:my_snippet
```

として, `$HOME/snippet/` に自分用 snippet 定義ファイルを置けば,
自分で定義した snippet が利用できます. 
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Vimのneosnippetで自分用snippetファイルを追加したい - 300億円欲しい](http://gg-hogehoge.hatenablog.com/entry/2014/04/05/230043)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}
```vim
:NeoSnippetEdit [{options}] [filetype]
		Opens the snippets for a given [filetype] to edit. It
		automatically selects the current buffer's filetype unless you
		specify another one by [filetype].

		If the path to [filetype] snippets is a directory, it
		automatically selects "[filetype].snip" in the [filetype]
		subdirectory.

		It edits a snippet file in |g:neosnippet#snippets_directory|
		with precedence. The snippets are re-cached automatically
		when you save the file after edit.

		The following parameters can be used as {options} to modify
		the behavior of the command. Note: You must escape spaces with
		a backslash "\".

		Note: You must set |g:neosnippet#snippets_directory| before using it.
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[neosnippet.vim/neosnippet.txt at master · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/blob/master/doc/neosnippet.txt)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


まずは自分のスニペットを置くディレクトリを指定する必要があるようだ。
そのためにまずはgithubリポジトリを作り、プラグインとしてインストールする。

githubにリポジトリを作ったあと、 _lazy.toml_ に

```toml
[[plugins]]
repo = 'leico/neosnippet-cpp'
```

を追加、その後

```vim
:call dein#install()
```

をvim上で実行。

その後 `g:neosnippet#snippets_directory`の設定を行う。
他のスニペットと共存できるように、以下のような設定を _lazy.toml_ に追加した。

```toml
[[plugins]]
repo = 'leico/neosnippet-cpp'
on_ft   = ['c', 'cpp']
depends = ['neosnippet.vim']
hook_source = '''
let g:neosnippet#snippets_directory = '~/.config/nvim/dein/repos/github.com/leico/neosnippet-cpp/snippets'
'''
```

* [dein.vim:tomlファイルでプラグイン管理する]({{site.github.url}}{% link _docs/VimR/VimR-dein-toml.md %})

を参考に

* c/c++ ファイルの時に読み込み
* neosnippet が必要
* プログラムが読み込まれる直前にディレクトリ指定

とした。他のファイルが開かれたらスニペットディレクトリが変更されるだろうと予想している。


## 既存のスニペットとの重複を回避する

`#include` に対するスニペットが両方で、
同じ名前で定義されていた時に既存のスニペット含めたくさんの候補が表示されてしまった。
なので元から入っているスニペットを無効化することにした。


{% capture text %}

Please read the documentation.

It is not only for one filetype.

```vim
				*g:neosnippet#disable_runtime_snippets*
g:neosnippet#disable_runtime_snippets
		This is a dictionary variable which uses file types as key.
		If you set the value of a file type entry to 1, this prevents
		loading "neosnippets" directories from 'runtimepath'. This is
		very useful to prevent snippet conflicts between self defined
		snippet files and the built-in snippet files of neosnippet. If
		you use an "_" as key for an entry this will treat the value of
		the entry as default value for all file types.
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
by   [Shougo (Shougo)](https://github.com/Shougo)
from [Ignoring/overriding specified snippets · Issue #429 · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/issues/429)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ドキュメントを漁ってみると

{% capture text %}
```vim
		For example:
>
		let g:neosnippet#disable_runtime_snippets = {
		\   'c' : 1, 'cpp' : 1,
		\ }

		" which disables all runtime snippets
		let g:neosnippet#disable_runtime_snippets = {
		\   '_' : 1,
		\ }
<
The default value is {}.
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[neosnippet.vim/neosnippet.txt at master · Shougo/neosnippet.vim](https://github.com/Shougo/neosnippet.vim/blob/master/doc/neosnippet.txt)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

```vim
let g:neosnippet#disable_runtime_snippets = {
\   'c' : 1, 'cpp' : 1,
\ }
```

ほぼ答えが書いてあった。のでこれを _lazy.toml_ に追加する。

```toml
[[plugins]]
repo = 'leico/neosnippet-cpp'
on_ft   = ['c', 'cpp']
depends = ['neosnippet.vim']
hook_source = '''
let g:neosnippet#snippets_directory = '~/.config/nvim/dein/repos/github.com/leico/neosnippet-cpp/snippets'
let g:neosnippet#disable_runtime_snippets = { 'c' : 1, 'cpp' : 1 }
'''
```

## スニペットの書き方

{% capture text %}

formatは次のようになる。

```
snippet [name]
abbr    [abbreviation]
alias   [aliases]
regexp  [pattern]
options [options]
  if ${1:conditions}
    ${2}
  endif
```

`snippet [name]` (必須)
: `[name]` に snippet を展開するための Keyword を書く。
  `[name]` に書かれた文字に対して、 snippet の展開を実行する。
  NeoSnippet なら `Ctrl+k` で展開する。

`abbr [abbreviation]`
: 補完の Popup に表示される説明文

`alias [aliases]`
: `[name]` の別名を複数つけられる。

`regexp [pattern]`
: `[pattern]` にマッチしたときのみ、`[name]` を展開する。`[pattern]` には正規表現を記述する。
  例えば、行の（空白を除いた）先頭の `[name]` のみを展開する場合は
  
  ```
    regexp '^\s*'
  ```

  と書く。

`options [options]`
: 幾つかの便利なオプションが提供されている。`[options]`が取りうる値としては、

    `word`
    : 単語の区切りで `[name]` を見つける。options wordなしだと
  
      ```
      hoge1+hoge2
      ```
  
      上記で hoge2 の snippet が登録されていても展開されない。
      (hoge1+hoge2で `[name]` だとみなされる)
      1単語は英数字とアンダーバーのつながりで判断されるので、
      上の場合だと + で別の単語と認識されることになる。
  
    `head`
    : 行頭に書かれた `[name]` にのみに snippet を適用する。ただし、空白などは除く。
    
    `indent`
    : snippet 展開後の文字に indent を適用する。

snippetの中身(必須）
:   
    ```js
    if ${1:condition1}
      ${2:#:statement}
    endif
    if $1 AND ${3:condition2}
      ${4:TARGET}
    endif
    ${5}
    ```
    `${}` で囲まれた部分は _place holder_ と呼ばれるもので、
    snippet 展開後 `Ctrl+k` でその場所に移動できる。
    _place holder_ の種類としては、

    `${number:text}`
    : `number` は飛ぶ順番、`text` はその場所にデフォルトで表示される文字列。
      変更が必要な場合はtextを上書きすることになる。

    `${number:#:text}`
    : `#` が付いていないものとの違いは、`text` を変更せずに `Ctrl+k` で
      次の _place holder_ にジャンプした際に `text` が消されるという点。

    `${number:TARGET}`
    : visualモードで選択中のテキストをここに挿入できる。この機能を使うためには、

      ```vim
      <Plug>(neosnippet_expand_target)
      ```

      でsnippetを展開するようkey mappingしないとだめ。

    `${number}`
    : ただのジャンプ先として便利。
      例えばifやforの中から抜けるための _place holder_ として使う。

    `$number`
    : ジャンプ先ではないが、同じ番号の _place holder_ で入力された文字がここにも入力される。
      for文とかで次のように使われる。

      ```js
      for (${1:i}; $1<${2:end}; $1++) {
        ${3:#:loop}
      }
      ${4}
      ```

    `${0}`
    : 最後にジャンプする場所。（number1 から順に飛んで最後に0に飛ぶ）

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[NeoSnippetのsnippetファイルの設定 - adragoonaの日記](http://d.hatena.ne.jp/adragoona/20130929/1380437722)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

neosnippetのヘルプも確認して、ほぼこれに従って記述すればよかった。




## C/C++の規則を見つける

問題はどのぐらいの粒度でスニペットを書くかということになる。
安直に考えると関数の場合

1. 定義
2. 前方宣言

の2種類を記述する必要がある。
なるべく柔軟にさまざまな記述に対応する・・・と考え、まずは最小限の記述からはじめた。すなわち

```c
#### char
snippet ''
alias   char_literal
abbr    charactor
  '${1:#:charactor}'${0}


#### string
snippet ""
alias   string_literal
abbr    string
  "${1:#:string}"${0}


#### array
snippet []
alias   array_literal
abbr    array
  [ ${1:#:number or contents} ]${0}


#### block
snippet {}
alias   block_literal
abbr    block/struct
  { ${1:#:contents} }${0}

#### bracket
snippet ()
alias   bracket_literal
abbr    bracket
  ( ${1:#:contents} )${0}

#### angle bracket
snippet <>
alias   angle_bracket_literal
abbr    angle bracket literal
< ${1:#:contents} >${0}
```

これらを作成した。C/C++は

```
[予約語] [左括弧][右括弧]
```

という構造ということに気づいた。

あとは延々と予約語を定義していくのだが、まずもって必ずブラケットが入る、
という部分に関しては先に記述してしまっている。

```c
#### sizeof
snippet sizeof
alias   size
sizeof( ${1:#:type} )${0}

## if
snippet if
options head
abbr    single line if statement
if( ${1:#:condition} ) ${2:#:process}
```

`if` や `sizeof`の後ろには必ず`()`が入るのでここは入力している。

ブラケットが一意に決まらない場合は都度選択する。

```c
#### include 
snippet #inc
alias   inc
options head
abbr    C style include
  #include ${1:#:header file}${0} 


## if
snippet if
options head
abbr    single line if statement
if( ${1:#:condition} ) ${2:#:process}
```

`#include`の場合、`#include<...>` と `#include "..."` のパターンがあるため
ブラケットを入力していない。
`if`文も

```c
if ( Condition )
  //process
```

```c
if ( Condition ){
  //process
}
```

の2パターンがあるので`{}`の入力はしていない。
