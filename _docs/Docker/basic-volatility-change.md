---
layout : post
title  : "Dockerに慣れる : 一時的にコンテナ内の情報を変更する"
date   : 2020/06/24
lastchange : 2020-06-25 01:48:27
tags   :
  - docker
  - httpd
  - container
  - docker exec
---

## コンテナ内を変更したい

[Dockerに慣れる : コンテナを操作する]({{site.github.url}}{% link _docs/Docker/docker-php.md %}) で起動と終了とプロセス削除まで行うことができた。ひとまず Hello world ができた感じがするので、次は内部に変更を加えてみる。
ただし、内部の変更はコンテナを再起動させてしまったら消えてしまうらしい。

{% capture text %}

Dockerコンテナは「スナップショット」とよばれるイメージファイルとしてその状態を保持しています。

Dockerイメージは「ファイルシステム全体の状態を保持したスナップショット」であり、
runコマンドでそのスナップショットをコンテナとして再現しています。


起動したコンテナはstop中も状態保持されていますが、rmするとスナップショットごと
破棄されます。

つまり、コンテナに内部変更を行いそれを残したい場合には、「新たなイメージとして保存」する必要があります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/08/docker-3/ "【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このページで紹介されている新しいイメージとして保存する方法は、ある程度修正や機能追加が完了した後、
つまりサービスとしてコンテナを公開する時に利用することになりそうだ。
この方法以外に、


{% capture text %}

今回は、Dockerにおける「マウント」について確認していきます。

「マウント」とは、ホスト側（コンテナの外）にあるディレクトリ・ファイルを、コンテナの中から利用できるようにする機能です。

マウントには３つの種類があります。

１．バインドマウント（bind）
: Dockerホストのファイルやディレクトリをコンテナ上にマウントする機能です。
  バインドマウントを行うと、コンテナの外にあるファイルを、コンテナの中から読み書き可能になります。

２．ボリュームマウント（volume）
: Docker が管理するデータ領域を コンテナ上にマウントする機能です。
  バインドマウントとの違いは、バインドマウントが「Dockerが管理するデータ領域以外もマウント可能である」ことに対し、
  ボリュームマウントは「Dockerが管理するデータ領域内でのマウントのみ可能である」という点です。

３．一時ファイルシステムのマウント（tmpfs）
: Dockerホストにファイルとして保存したくないデータを一時的に利用できるようにする（データ領域をメモリ上に置く）機能です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



と、コンテナ外のディレクトリを共有してコンテナの起動・終了の影響を受けないようにする方法があるようだ。

この記事ではコンテナ内部の変更と、再度コンテナプロセスを動作させた時に変更が消去されていることを確認する。


参考:

* [【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/08/docker-3/ "【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
* [【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
* [docker exec -itって実際は何をしてるの？【90日目】 - エンジニアのひよこ_level10](https://www.nyamucoro.com/entry/2018/01/11/224932 "docker exec -itって実際は何をしてるの？【90日目】 - エンジニアのひよこ_level10")
* `httpd -h`
* [Apacheでhttpd: Could not reliably determine the server's fully qualified domain nameと言われる解決方法 - Qiita](https://qiita.com/pugiemonn/items/41d4bebf48437eed3f4c "Apacheでhttpd: Could not reliably determine the server's fully qualified domain nameと言われる解決方法 - Qiita")
* [Dockerコンテナの作成、起動〜停止まで - Qiita](https://qiita.com/kooohei/items/0e788a2ce8c30f9dba53 "Dockerコンテナの作成、起動〜停止まで - Qiita")
* [Dockerコンテナからのデタッチ \| technote](https://tech.withsin.net/2015/09/30/docker-container-detach/ "Dockerコンテナからのデタッチ \| technote")



 


## コンテナ内の編集をする


{% capture text %}

まずはコンテナイメージから、httpdコンテナを立ち上げます。

```sh
$ docker run -d -p 8080:80 httpd
```
___

起動したらpsコマンドで、httpdコンテナのIDを確認します。

```sh
$ docker ps
```

```sh
CONTAINER ID 　IMAGE 　COMMAND 　　　　　　 CREATED 　　　　　　　STATUS 　　　　　　　　PORTS 　　　　　　　　　　NAMES
59f1b165c15d   httpd  "httpd-foreground" 3 seconds ago       Up 2 seconds        0.0.0.0:8080->80/tcp   relaxed_mcclintock
```

現在バックグラウンドで起動している状態ですので、execコマンドでコンテナの内部に入ります（指定したコンテナの中でbashプロセスを実行する）。

```sh
docker exec -it 59f1b165c15d /bin/bash
```

次にWebページを編集するのですが、現在の状態ではコンテナ上にエディタがインストールされていないので、先にインストールを行っておきます。

```sh
apt-get update
apt-get install vim
```

エディタの準備ができたら、index.htmlを以下（黄色字の内容）の様に編集してみます。

```sh
vi /usr/local/apache2/htdocs/index.html
```

```html
<html><body><h1>httpd container working!</h1></body></html>
```

編集を保存し、httpdを再起動します。

```sh
/usr/local/apache2/bin/httpd restart
```

Webブラウザでページを確認すると、編集内容で更新されているはずです。

ですが、この編集内容は「このコンテナが生きている間だけ」有効なものであり、
コンテナをrmコマンドで破棄してしまうと、先ほどの編集内容（＆インストールしたエディタ）も消失します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/08/docker-3/ "【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

まずは `httpd` のコンテナを起動する。

```sh
$ docker run -d -p 8080:80 httpd
32c3476e608722c2489dd4dcb9ed98ae5378e55a43660fb9e94f4b66d8ab6897
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
32c3476e6087        httpd               "httpd-foreground"   7 seconds ago       Up 7 seconds        0.0.0.0:8080->80/tcp   unruffled_easley
```

起動確認した。このコンテナ内部のシェルを実行し、コンテナ内部のファイルを編集する。
今回はコンテナ ID ではなく、名前を使ってやってみる。

```sh
$ docker exec -it unruffled_easley /bin/bash
root@32c3476e6087:/usr/local/apache2# 
```

内部に入れた。この時点でのディレクトリが既に _/usr/local/apache2_ になっていた。

引数の `-it` は

{% capture text %}
## -it

-iは、 `Keep STDIN open even if not attached`
標準入力を開き続ける。

-tは、 `Allocate a pseudo-TTY`
疑似ttyを割りあてる。

標準入力を開き続け、そこを操作出来るようにする。
→手元の環境で、docker内入力ができるようにする
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[docker exec -itって実際は何をしてるの？【90日目】 - エンジニアのひよこ_level10](https://www.nyamucoro.com/entry/2018/01/11/224932 "docker exec -itって実際は何をしてるの？【90日目】 - エンジニアのひよこ_level10")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということだそうだ。

次に、コンテナ内部のパッケージ郡をアップデートする。これには `apt` を用いた。
まずはパッケージリストをアップデートする。

```sh
# apt update
Get:1 http://security.debian.org/debian-security buster/updates InRelease [65.4 kB]
Get:2 http://deb.debian.org/debian buster InRelease [121 kB]
Get:3 http://deb.debian.org/debian buster-updates InRelease [51.9 kB]
Get:4 http://security.debian.org/debian-security buster/updates/main amd64 Packages [204 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 Packages [7905 kB]
Get:6 http://deb.debian.org/debian buster-updates/main amd64 Packages [7868 B]
Fetched 8355 kB in 3s (3191 kB/s)                         
Reading package lists... Done
Building dependency tree       
Reading state information... Done
1 package can be upgraded. Run 'apt list --upgradable' to see it.
```

アップグレードできるパッケージを確認する。

```sh
# apt list --upgradable
Listing... Done
linux-libc-dev/stable 4.19.118-2+deb10u1 amd64 [upgradable from: 4.19.118-2]
N: There is 1 additional version. Please use the '-a' switch to see it
```

アップグレードできるパッケージを全部表示する。

```sh
# apt list --upgradable -a
Listing... Done
linux-libc-dev/stable 4.19.118-2+deb10u1 amd64 [upgradable from: 4.19.118-2]
linux-libc-dev/stable,now 4.19.118-2 amd64 [installed,upgradable to: 4.19.118-2+deb10u1]
```

アップグレードする。

```sh
# apt upgrade
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following package was automatically installed and is no longer required:
  lsb-base
Use 'apt autoremove' to remove it.
The following packages will be upgraded:
  linux-libc-dev
1 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Need to get 1354 kB of archives.
After this operation, 1024 B of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://security.debian.org/debian-security buster/updates/main amd64 linux-libc-dev amd64 4.19.118-2+deb10u1 [1354 kB]
Fetched 1354 kB in 0s (4961 kB/s)       
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 9613 files and directories currently installed.)
Preparing to unpack .../linux-libc-dev_4.19.118-2+deb10u1_amd64.deb ...
Unpacking linux-libc-dev:amd64 (4.19.118-2+deb10u1) over (4.19.118-2) ...
Setting up linux-libc-dev:amd64 (4.19.118-2+deb10u1) ...
```

`vim` をインストールする。

```sh
2# apt install vim
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following package was automatically installed and is no longer required:
  lsb-base
Use 'apt autoremove' to remove it.
The following additional packages will be installed:
  libgpm2 vim-common vim-runtime xxd
Suggested packages:
  gpm ctags vim-doc vim-scripts
The following NEW packages will be installed:
  libgpm2 vim vim-common vim-runtime xxd
0 upgraded, 5 newly installed, 0 to remove and 0 not upgraded.
Need to get 7425 kB of archives.
After this operation, 33.8 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://deb.debian.org/debian buster/main amd64 xxd amd64 2:8.1.0875-5 [140 kB]
Get:2 http://deb.debian.org/debian buster/main amd64 vim-common all 2:8.1.0875-5 [195 kB]
Get:3 http://deb.debian.org/debian buster/main amd64 libgpm2 amd64 1.20.7-5 [35.1 kB]
Get:4 http://deb.debian.org/debian buster/main amd64 vim-runtime all 2:8.1.0875-5 [5775 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 vim amd64 2:8.1.0875-5 [1280 kB]
Fetched 7425 kB in 1s (6909 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package xxd.
(Reading database ... 9613 files and directories currently installed.)
Preparing to unpack .../xxd_2%3a8.1.0875-5_amd64.deb ...
Unpacking xxd (2:8.1.0875-5) ...
Selecting previously unselected package vim-common.
Preparing to unpack .../vim-common_2%3a8.1.0875-5_all.deb ...
Unpacking vim-common (2:8.1.0875-5) ...
Selecting previously unselected package libgpm2:amd64.
Preparing to unpack .../libgpm2_1.20.7-5_amd64.deb ...
Unpacking libgpm2:amd64 (1.20.7-5) ...
Selecting previously unselected package vim-runtime.
Preparing to unpack .../vim-runtime_2%3a8.1.0875-5_all.deb ...
Adding 'diversion of /usr/share/vim/vim81/doc/help.txt to /usr/share/vim/vim81/doc/help.txt.vim-tiny by vim-runtime'
Adding 'diversion of /usr/share/vim/vim81/doc/tags to /usr/share/vim/vim81/doc/tags.vim-tiny by vim-runtime'
Unpacking vim-runtime (2:8.1.0875-5) ...
Selecting previously unselected package vim.
Preparing to unpack .../vim_2%3a8.1.0875-5_amd64.deb ...
Unpacking vim (2:8.1.0875-5) ...
Setting up libgpm2:amd64 (1.20.7-5) ...
Setting up xxd (2:8.1.0875-5) ...
Setting up vim-common (2:8.1.0875-5) ...
Setting up vim-runtime (2:8.1.0875-5) ...
Setting up vim (2:8.1.0875-5) ...
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/vim (vim) in auto mode
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/vimdiff (vimdiff) in auto mode
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/rvim (rvim) in auto mode
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/rview (rview) in auto mode
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/vi (vi) in auto mode
......(中略).....
Processing triggers for libc-bin (2.28-10) ...
```

_index.html_ を編集する。

```sh
# vim htdocs/index.html
```
```html
<html><body><h1>httpd cotainer working!</h1></body></html>
```

`httpd` を再起動する。

```sh
# /usr/local/apache2/bin/httpd -k restart
```

引用元の方法と引数が異なっている。


{% capture text %}
```sh
Usage: /usr/local/apache2/bin/httpd [-D name] [-d directory] [-f file]
                                    [-C "directive"] [-c "directive"]
                                    [-k start|restart|graceful|graceful-stop|stop]
                                    [-v] [-V] [-h] [-l] [-L] [-t] [-T] [-S] [-X]
Options:
  -D name            : define a name for use in <IfDefine name> directives
  -d directory       : specify an alternate initial ServerRoot
  -f file            : specify an alternate ServerConfigFile
  -C "directive"     : process directive before reading config files
  -c "directive"     : process directive after reading config files
  -e level           : show startup errors of level (see LogLevel)
  -E file            : log startup errors to file
  -v                 : show version number
  -V                 : show compile settings
  -h                 : list available command line options (this page)
  -l                 : list compiled in modules
  -L                 : list available configuration directives
  -t -D DUMP_VHOSTS  : show parsed vhost settings
  -t -D DUMP_RUN_CFG : show parsed run settings
  -S                 : a synonym for -t -D DUMP_VHOSTS -D DUMP_RUN_CFG
  -t -D DUMP_MODULES : show all loaded modules 
  -M                 : a synonym for -t -D DUMP_MODULES
  -t -D DUMP_INCLUDES: show all included configuration files
  -t                 : run syntax check for config files
  -T                 : start without DocumentRoot(s) check
  -X                 : debug mode (only one worker, do not detach)
  ```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
`httpd -h`
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この内容から `-k` オプションを追加した。
ところが、以下の警告がでた。

```sh
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
```

{% capture text %}
httpd.conf に ServerName を書けばいいみたいです。

```conf
vi /etc/httpd/conf/httpd.conf

ServerName hogepiyo:80
```

ServerName に hogepiyo のように何か適当な文字列を書きます。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Apacheでhttpd: Could not reliably determine the server's fully qualified domain nameと言われる解決方法 - Qiita](https://qiita.com/pugiemonn/items/41d4bebf48437eed3f4c "Apacheでhttpd: Could not reliably determine the server's fully qualified domain nameと言われる解決方法 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということなので、 _conf/httpd.conf_ を編集する。

```sh
# vim conf/httpd.conf 
```

```conf
...(中略)...
#
# ServerName gives the name and port that the server uses to identify itself.
# This can often be determined automatically, but we recommend you specify
# it explicitly to prevent problems during startup.
#
# If your host doesn't have a registered DNS name, enter its IP address here.
#
#ServerName www.example.com:80
ServerName test
...(中略)...
```

もう一度再起動させてみる。

```sh
# /usr/local/apache2/bin/httpd -k restart
#
```

エラーなく再起動できた。ブラウザで確認してみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-volatility-change/01_index.png %}{% endcapture %}
{% capture caption %}
_index.html_ の内容変更が反映された。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

コンテナのターミナルからホストに戻るのだが、


{% capture text %}
`docker attach` 後に `exit` コマンドで抜けると停止します。

```sh
root@0b5aad08487b:/# exit
exit
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Dockerコンテナの作成、起動〜停止まで - Qiita](https://qiita.com/kooohei/items/0e788a2ce8c30f9dba53 "Dockerコンテナの作成、起動〜停止まで - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`exit` で抜けてしまうと、コンテナが停止してしまう。

{% capture text %}
Dockerコンテナで作業をしていて、コンテナから抜けたいが、単に `control-D` で抜けようとすると 
**logout** になりコンテナは終了してしまう。これを回避する方法である。

```
[control-P] [control-Q]
```

上記でコンテナからデタッチすることができる。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Dockerコンテナからのデタッチ \| technote](https://tech.withsin.net/2015/09/30/docker-container-detach/ "Dockerコンテナからのデタッチ \| technote")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

デタッチすることでホストに戻ることができるらしい。やってみる。

```sh
# read escape sequence
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
32c3476e6087        httpd               "httpd-foreground"   6 hours ago         Up 6 hours          0.0.0.0:8080->80/tcp   unruffled_easley
```

できたらしい。

実際に変更が消えてしまうか確認する。 `httpd` を終了させてプロセスを消去する。

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
32c3476e6087        httpd               "httpd-foreground"   6 hours ago         Up 6 hours          0.0.0.0:8080->80/tcp   unruffled_easley
$ docker stop unruffled_easley
unruffled_easley
$ docker rm unruffled_easley
unruffled_easley
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

そしてもう一度プロセスを実行する。

```sh
$ docker run -d -p 8080:80 httpd
7138649b52849998d5733b7b6461e7b0f6710b1fc03533323c7ae79d82586e9c
```

ブラウザでアクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-volatility-change/02_revert.png %}{% endcapture %}
{% capture caption %}
_index.html_ がもとに戻った。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}
