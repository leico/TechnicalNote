---
layout : post
title  : "dein.vim:tomlファイルからローカルに置いたプラグインを読み込む"
date : 2020/01/29
lastchange : 2020-01-29 20:35:57.
tags   :
  - vim
  - VimR
  - vimrc
  - init.vim
  - dein
  - toml
---

## github にアップされているプラグインに関しては問題ないが・・・

ローカルに置いたプラグインも toml で管理できるのかわからなかったのでやってみました。
ソースを読んだりしていけそうだったのでやってみたら動きました。

参考:

* [dein.vim/parse.vim at 9a8382d7729344cf141cac2ed40edc15b97349e4 · Shougo/dein.vim](https://github.com/Shougo/dein.vim/blob/9a8382d7729344cf141cac2ed40edc15b97349e4/autoload/dein/parse.vim)
* [VimでSwiftのSyntax Highlight - Qiita](https://qiita.com/shotat/items/7302f937ae623ccc154a)
* [apple/swift: The Swift Programming Language](https://github.com/apple/swift)
* [dein.vimでローカルに配置したプラグインを読み込む - vimemo](https://scrapbox.io/vimemo/dein.vim%E3%81%A7%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%AB%E9%85%8D%E7%BD%AE%E3%81%97%E3%81%9F%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3%E3%82%92%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%82%80)
* [サブフォルダを新規リポジトリに分割する - GitHub ヘルプ](https://help.github.com/ja/github/using-git/splitting-a-subfolder-out-into-a-new-repository)
* [git最強のオプション filter-branch - Qiita](https://qiita.com/Spring_MT/items/f60c391b5dbf569a1d12)
* [dein.vim/dein.txt at master · Shougo/dein.vim](https://github.com/Shougo/dein.vim/blob/master/doc/dein.txt)



## swift がシンタックスハイライトされない

さすがにそろそろシンタックスハイライトが欲しいなと思って調べてみたのですが、

{% capture text %}

以下、NeoBundleの設定です。
余談ですが NeoBundle から dein.vim に乗り換えると闇の力を得られるそうです。

リポジトリを直接指定できないので、手動でディレクトリを掘りましょう。

簡単のため、こんな感じで

```sh
$ mkdir ~/.vim/bundle/manual
```

ディレクトリ名は何でも大丈夫です。

.vimrc に以下を記述します。

```vim
NeoBundle 'apple-swift', {'type': 'nosync', 'base': '~/.vim/bundle/manual'} 
```

今回は `apple-swift` にしてみました。ここは何でも大丈夫です。
`base` 以下もお好みで大丈夫です。

`base` で指定したディレクトリに移動し、cloneします

```sh
$ cd ~/.vim/bundle/manual
$ git clone git@github.com:apple/swift.git apple-swift
```

結構時間かかります。

終わったら以下のコマンドを打ちます。
swift.gitリポジトリからutils/vimだけ取り出します。

```sh
$ cd apple-swift
$ git filter-branch --subdirectory-filter utils/vim HEAD
```

これで Syntax highlight が動きます。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[VimでSwiftのSyntax Highlight - Qiita](https://qiita.com/shotat/items/7302f937ae623ccc154a)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このように、 NeoBundle での設定しか出てきませんでした。
普通に **dein.vim** を利用するだけであれば、上記に加え、 _init.vim_ に

```vim
call dein#add('~/.vim/bundle/apple-swift')
```

で問題なさそうです。しかし toml でローカルのプラグインを
読み込むことってできるのか実証結果が見つかりません。

`git filter-branch --subdirectory-filter` も調べておいたほうがよさそうです。




## git のバージョン履歴を完全に書き換える




{% capture text %}

{:start="5"}
5. リポジトリのファイルからサブフォルダを抽出するには、
以下の情報を指定して `git filter-branch` を実行します。

    * `FOLDER-NAME` : 別のリポジトリの作成元にしたい、プロジェクト内のフォルダです。
    * `BRANCH-NAME` : `master` や `gh-pages` などの、現在のプロジェクトのデフォルトブランチです。
        ```sh
        $ git filter-branch --prune-empty --subdirectory-filter FOLDER-NAME  BRANCH-NAME 
        # Filter the specified branch in your directory and remove empty commits
        > Rewrite 48dc599c80e20527ed902928085e7861e6b3cbe6 (89/89)
        > Ref 'refs/heads/BRANCH-NAME' was rewritten
        ```

        これで、リポジトリにはサブフォルダ内にあったファイルのみが含まれることになります。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[サブフォルダを新規リポジトリに分割する - GitHub ヘルプ](https://help.github.com/ja/github/using-git/splitting-a-subfolder-out-into-a-new-repository)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






{% capture text %}

## filter-branchとは

これは、大量のコミットの書き換えを機械的に行うオプションです。
(filter-branch自体はシェルスクリプトで書かれています。)
これを使うとレポジトリの歴史上からコミットされたファイルを完全に抹消することができます！

今回、ファイルを抹消するために filter-branchの--index-filter オプションを使います。

## 使うシチュエーション

こんな怖いオプションどこで使うのかというと、例えば下記のようなシチュエーションが考えられます。

* パスワードファイルを間違ってcommitしてしまった or やんごとなき事情により削除したい
* 巨大なファイルを間違ってcommitしてしまった

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[git最強のオプション filter-branch - Qiita](https://qiita.com/Spring_MT/items/f60c391b5dbf569a1d12)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



github のヘルプはかなりフランクに書いてありますが、たいへんなプログラムのようです。
慎重に行います。



## 普通なローカルプラグインの読み込み

{% capture text %}
```sh
 dein#local({directory}, [{options}, [{names}]])
```

dein#local() で自分の書いたプラグインとかをおいてあるディレクトリを指定する
\_vimrc に以下のように記述する

```vim
 ...
 if dein#load_state(s:dein_dir)
     call dein#begin(s:dein_dir)
     ...
     
     call dein#local('~/vim/myplugins', 
                 \ {}, 
                 \ [
                 \   'scrapbox.vim',
                 \ ])
 
     call dein#end()
     call dein#save_state()
 endif
```

`scrapbox.vim` というプラグインを読み込んでくれる
第２引数にオプションを指定することも可能



{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[dein.vimでローカルに配置したプラグインを読み込む - vimemo](https://scrapbox.io/vimemo/dein.vim%E3%81%A7%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%AB%E9%85%8D%E7%BD%AE%E3%81%97%E3%81%9F%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3%E3%82%92%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%82%80)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


普通に読み込む場合はこのような実証ログを発見することができました。
しかし toml を利用した記事が見当たりません。

## 探してみる

{% capture text %}

dein#add({repo}[, {options}])
: Initialize a plugin.
    {repo} is the repository URI or local repository directory
    path.  If {repo} starts with github user name (ex:
    "Shougo/dein.vim"), dein will install github plugins.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[dein.vim/dein.txt at master · Shougo/dein.vim](https://github.com/Shougo/dein.vim/blob/master/doc/dein.txt)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このあたりとか、[この辺りのソースを流し読んでみました](https://github.com/Shougo/dein.vim/blob/9a8382d7729344cf141cac2ed40edc15b97349e4/autoload/dein/parse.vim)。
github の時と同様、 `repo = ` でローカルのディレクトリを指定してあげれば大丈夫そうです。

やってみます。以下を _dein.toml_ に追加しました。

```toml
# [local] apple-swift-syntax-----
[[plugins]]
repo   = '~/dotfiles/vim_localplugin/apple-swift-syntax'

```

セーブして再起動させます。

{% capture url %}{{site.github.url}}{% link _docs/VimR/images/localplugin-toml/01_highlight.png %}{% endcapture %}
{% capture caption %}
シンタックスハイライトが入った
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

無事入りました。

## shellscript で自動化

github からソースをとってきて filter-branch する過程を shell script にしました。

```sh
#!/bin/sh

localplugindir="$HOME/dotfiles/vim_localplugin";
swiftdir="${localplugindir}/apple-swift-syntax";

if [ -e $localplugindir ]; then
  rm -rf $localplugindir
fi

mkdir $localplugindir

cd $localplugindir

if [ -e $swiftdir ]; then
  rm -rf $swiftdir
fi

git clone https://github.com/apple/swift.git $swiftdir
cd $swiftdir
git filter-branch --subdirectory-filter utils/vim HEAD

```

これで環境が変わってもすぐ復元できそうです。
