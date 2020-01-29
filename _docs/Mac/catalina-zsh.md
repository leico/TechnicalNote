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

なんでだろうと思ったら、エラーメッセージが zsh だった。サクッと解決できたのでその記録。

参考:

* [新macOS Catalina が zsh を標準にするって聞いたので Mojave のまま移行してみた - Qiita](https://qiita.com/tkooler_lufar/items/66e8fa451185e812a33a)

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
