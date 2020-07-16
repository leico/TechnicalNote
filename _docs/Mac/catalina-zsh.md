---
layout : post
title  : Catalina から zsh になって bash_profile が利用できなくなった
date   : 2020/01/29
lastchange : 2020-01-29 18:14:04.
tags   :
  - macOS
  - catalina
  - zsh
  - bash
---

## エイリアスしていたコマンドが利用できない

なんでだろうと思ったら、エラーメッセージが zsh だった。サクッと解決できたのでその記録です。

参考:

* [新macOS Catalina が zsh を標準にするって聞いたので Mojave のまま移行してみた - Qiita](https://qiita.com/tkooler_lufar/items/66e8fa451185e812a33a)
* [.bashrc not executed when opening new terminal - Ask Ubuntu](https://askubuntu.com/questions/161249/bashrc-not-executed-when-opening-new-terminal/161274#161274 ".bashrc not executed when opening new terminal - Ask Ubuntu")
* [シェルの種類とバージョンの検出 - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog](https://fumiyas.github.io/2013/12/04/name-ver-mode.sh-advent-calendar.html "シェルの種類とバージョンの検出 - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog")
* [zshプロンプトのカスタマイズ - Qiita](https://qiita.com/yamagen0915/items/77fb78d9c73369c784da "zshプロンプトのカスタマイズ - Qiita")
* [zshのプロンプトを256色表示にする＋好きなフォーマットで時刻表示 - Qiita](https://qiita.com/butaosuinu/items/770a040bc9cfe22c71f4 "zshのプロンプトを256色表示にする＋好きなフォーマットで時刻表示 - Qiita")



## zsh 用の設定ファイルに _bash\_profile_ へのリンクを貼る


{% capture text %}

| bashで使ってたファイル | zshで使うファイル | 備考                                                                         |
|------------------------|-------------------|------------------------------------------------------------------------------|
| _~/.bash_profile_      | _~/.zprofile_     | `source .bashrc` してた場合、それより **前** の記述をコピペする              |
| _~/.bashrc_            | _~/.zshrc_        | bash のログインシェルは読まなかったが、 zsh はログインシェルでも読む点に注意 |
| _~/.bash_profile_      | _~/.zlogin_       | `source .bashrc` してた場合にのみ作る。それより **後** の記述をコピペする    |
| _~/.bash_logout_       | _~/.zlogout_      |                                                                              |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[新macOS Catalina が zsh を標準にするって聞いたので Mojave のまま移行してみた - Qiita](https://qiita.com/tkooler_lufar/items/66e8fa451185e812a33a)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

_.bashrc_ が絡むあたりでややこしいみたいですが、私の [_bash\_profile_](https://github.com/leico/dotfiles/blob/master/bash_profile) はそこまで大変なことをしていないので、
普通に[_bash\_profile_](https://github.com/leico/dotfiles/blob/master/bash_profile) へ _.zprofile_ からリンクを貼る形で解決させました。

```sh
ln -sf ~/dotfiles/bash_profile ~/.zprofile
```

これで今のところ問題なく動いています。




## `zsh` 用の設定と `bash` 用の設定を分ける

{% capture text %}

The standard `~/.profile` has this in it:

```sh
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[.bashrc not executed when opening new terminal - Ask Ubuntu](https://askubuntu.com/questions/161249/bashrc-not-executed-when-opening-new-terminal/161274#161274 ".bashrc not executed when opening new terminal - Ask Ubuntu")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}
スクリプトがどのシェルインタープリターで動いているかを判定したいときってありませんか? 私はあります。ポータブルなシェルスクリプトを書きたい、 しかし、互換性の問題などでシェルの種類に依存するコードを書き分けないといけない場合です。

ざっくりとシェルの種類、バージョン、 動作モードを判定するスクリプトを作ってみました: 
[sh-detect-name-ver-mode.sh](https://github.com/fumiyas/fumiyas.github.io/blob/master/2013/12/04/sh-detect-name-ver-mode.sh)

```sh
if [ -n "${BASH_VERSION-}" ]; then

~~~(中略)~~~

elif [ -n "${ZSH_VERSION-}" ]; then
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[シェルの種類とバージョンの検出 - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog](https://fumiyas.github.io/2013/12/04/name-ver-mode.sh-advent-calendar.html "シェルの種類とバージョンの検出 - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`$ZSH_VERSIONS` や `$BASH_VERSION` というシェル変数を利用することで場合分けをすることができるようです。


## プロンプトの表示を色付けする

{% capture text %}

# 使用できる特殊文字

| 文字 | 出力                 |
|------|----------------------|
| %n   | ユーザー名           |
| %m   | ホスト名             |
| %~   | カレントディレクトリ |
| %T   | 時間(HH:MM)          |
| %*   | 時間(HH:MM:SS)       |
| %D   | 日付(YY-MM-DD)       |

# 改行を入れる

文字列中に改行をいれると表示も改行される

```sh
PROMPT='%n
%m $ '
```

表示結果

```
user_name
host_name $ 
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[zshプロンプトのカスタマイズ - Qiita](https://qiita.com/yamagen0915/items/77fb78d9c73369c784da "zshプロンプトのカスタマイズ - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

### Zshパラメータ利用

例2

```sh
PROMPT="%K{082}%F{001}[%n@%m]%f%k"
```

もう一つは `man zshmisc` で見れるZshのパラメータを利用した方法。
`%K{色番号}` で背景色、 `%F{色番号}` で文字色を指定、 `%k` で背景色リセット、 `%f` で文字色リセット出来ます。
こちらのほうがスッキリしていて見やすいので僕はこちらで指定しています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[zshのプロンプトを256色表示にする＋好きなフォーマットで時刻表示 - Qiita](https://qiita.com/butaosuinu/items/770a040bc9cfe22c71f4 "zshのプロンプトを256色表示にする＋好きなフォーマットで時刻表示 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この辺りを利用して、以下のような設定を _.zlogin_ 記述しました。

```sh
# zsh prompt setting
PROMPT='%K{002}%m%k%K{003}@%n%k %D %T
%K{007}%F{000}%~%f%k
$ '
```

1行目
: `host@user [日付] [時間]`

2行目
: `[ディレクトリパス]`

3行目
: `[コマンド入力]`

となっています。