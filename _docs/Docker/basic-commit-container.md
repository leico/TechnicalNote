---
layout : post
title  : "Dockerに慣れる : 変更したコンテナイメージを保存する"
date   : 2020/06/25
lastchange : 2020-06-25 12:13:31
tags   :
  - docker
  - httpd
  - container
  - docker commit
---

## コンテナ内を変更してももとに戻ってしまう

[Dockerに慣れる : 一時的にコンテナ内の情報を変更する]({{site.github.url}}{% link _docs/Docker/docker-php.md %})
でコンテナ内部の shell を呼び出し変更することができたが、参考にしたサイトに書いてある通り
プロセスを消去すると変更した内容が元に戻ってしまった。

今回は同じ参考サイトに書いてあったように、コンテナイメージの保存を試してみる。

参考:

* [【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/08/docker-3/ "【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")

## コンテナイメージを保存する

{% capture text %}

以下の様に「保存したいコンテナID、「保存イメージ名：タグ」を指定してcommitコマンドを発行します。

```sh
docker commit 59f1b165c15d httpd_test:0.0.1
```

imagesコマンドでコンテナイメージを確認すると、「httpd_test」という名前で新しいコンテナイメージが作成されています。

```sh
docker images
```
 
```sh
REPOSITORY  TAG    IMAGE ID     CREATED       SIZE
httpd_test  0.0.1  dbd3b7a24de7 6 seconds ago 179MB
httpd       latest 2a51bb06dc8b 5 weeks ago   132MB
hello-world latest 4ab4c602aa5e 3 months ago  1.84kB
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/08/docker-3/ "【Docker】第3回　コンテナのライフサイクルとイメージの作成/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

変更をして commit を試す。まずは起動する。

```sh
$ docker run -d -p 8080:80 httpd
fecf592b79b6870e3c41424c3ea475f903b6b9b418f9aa0ff83d30deeb9f0133
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
fecf592b79b6        httpd               "httpd-foreground"   26 seconds ago      Up 25 seconds       0.0.0.0:8080->80/tcp   sad_ardinghelli
```

ブラウザでアクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/01_before.png %}{% endcapture %}
{% capture caption %}
最初の状態
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


コンテナ内の shell に入る

```sh
$ docker exec -it sad_ardinghelli /bin/bash
root@fecf592b79b6:/usr/local/apache2# 
```

パッケージのアップデート

```sh
# apt update && apt upgrade
Get:1 http://deb.debian.org/debian buster InRelease [121 kB]
Get:2 http://deb.debian.org/debian buster-updates InRelease [51.9 kB]
Get:3 http://deb.debian.org/debian buster/main amd64 Packages [7905 kB]
Get:4 http://security.debian.org/debian-security buster/updates InRelease [65.4 kB]
Get:5 http://security.debian.org/debian-security buster/updates/main amd64 Packages [204 kB]
Get:6 http://deb.debian.org/debian buster-updates/main amd64 Packages [7868 B]
Fetched 8355 kB in 3s (2968 kB/s)                         
Reading package lists... Done
Building dependency tree       
Reading state information... Done
1 package can be upgraded. Run 'apt list --upgradable' to see it.
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
Fetched 1354 kB in 0s (4677 kB/s)       
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 9613 files and directories currently installed.)
Preparing to unpack .../linux-libc-dev_4.19.118-2+deb10u1_amd64.deb ...
Unpacking linux-libc-dev:amd64 (4.19.118-2+deb10u1) over (4.19.118-2) ...
Setting up linux-libc-dev:amd64 (4.19.118-2+deb10u1) ...
#
```

`vim` を入れる

```sh
# apt install vim
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
Fetched 7425 kB in 1s (5417 kB/s)
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
...(中略)...
Processing triggers for libc-bin (2.28-10) ...
#
```

_index.html_ を変更する

```sh
# vim htdocs/index.html
```

```html
<html><body><h1>It was changed!!!</h1></body></html>
```

`httpd` を再起動させる。警告が出るが、原因が前回の時にわかっているので今回はスルー。

```sh
# bin/httpd -k restart
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
```


再度ブラウザからアクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/02_changed.png %}{% endcapture %}
{% capture caption %}
変更をした状態
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`ctrl-p`  + `ctrl-q`  を押してデタッチ。コンテナのターミナルからホストに戻る。

```sh
# read escape sequence
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
fecf592b79b6        httpd               "httpd-foreground"   14 minutes ago      Up 14 minutes       0.0.0.0:8080->80/tcp   sad_ardinghelli
```

いよいよこれをイメージとして保存する。

```sh
$ docker commit sad_ardinghelli httpd_test:0.0.1
sha256:9ae8cc7c63757925229884abe20d2214be71cb2a6be468aec41fc8fb52760580
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.1               9ae8cc7c6375        5 seconds ago       221MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

`httpd_test` イメージができた。

`httpd` のプロセスを消去して `httpd_test` コンテナを実行してみる。

```sh
 $ docker stop sad_ardinghelli && docker rm sad_ardinghelli
sad_ardinghelli
sad_ardinghelli
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker run -d -p 8080:80 httpd_test
Unable to find image 'httpd_test:latest' locally
docker: Error response from daemon: pull access denied for httpd_test, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
```

怒られた。エラーを読んでみると `httpd_test:latest` を探しに行って、見つからなかったらしい。
先程保存したのは `httpd_test:0.0.1` なので、バージョンまで指定して実行してみる。

```sh
 $ docker run -d -p 8080:80 httpd_test:0.0.1
bff77eacbaee92dfd5ad7f9e1c888d87b984ab6dab3593fe021abfc15349bd42
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
bff77eacbaee        httpd_test:0.0.1    "httpd-foreground"   3 minutes ago       Up 3 minutes        0.0.0.0:8080->80/tcp   cranky_goldwasser
```

起動したらしい。ブラウザで確認してみる。


{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/03_httpd_test.png %}{% endcapture %}
{% capture caption %}
先程の状態で起動した
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

これを変更して、さらに別バージョンでコミットしてみる。重複かつ長いのでログ等は省略。

```sh
# apt update && apt upgrade
# vim htdocs/index.html
```

```html
<html><body><h1>this file was modified!!!</h1></body></html>
```

```sh
# bin/httpd -k restart
```

ブラウザでアクセスする

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/04_commit_changed.png %}{% endcapture %}
{% capture caption %}
さらなる変更が反映された
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ホストのターミナルに戻る

```sh
# read escape sequence
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
bff77eacbaee        httpd_test:0.0.1    "httpd-foreground"   10 minutes ago      Up 10 minutes       0.0.0.0:8080->80/tcp   cranky_goldwasser
```

バージョンを新しくしてイメージに保存する

```sh
$ docker commit cranky_goldwasser httpd_test:0.0.2
sha256:e51586e6a3bffe7a405af9311a1b319807770e5e0fbccf6c08776ddbcc012064
lev@leico_studio ~/Project/public_git/sandbox $ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.2               e51586e6a3bf        18 seconds ago      221MB
httpd_test          0.0.1               9ae8cc7c6375        16 minutes ago      221MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

`httpd_test:0.0.2` ができた。 `httpd_test:0.0.1`  を終了して `httpd_test:0.0.2` を立ち上げてみる。

```sh
$ docker stop cranky_goldwasser && docker rm cranky_goldwasser && docker run -d -p 8080:80 httpd_test:0.0.2
cranky_goldwasser
cranky_goldwasser
bb1caef8231926c56a91ef58044a058dad049728fa41948961abc40f9715af8c
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
bb1caef82319        httpd_test:0.0.2    "httpd-foreground"   6 seconds ago       Up 5 seconds        0.0.0.0:8080->80/tcp   adoring_tharp
```

ブラウザからアクセスしてみる

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/05_0.0.2.png %}{% endcapture %}
{% capture caption %}
変更が保持されている
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

`httpd_test:0.0.1` を _8090_ ポートで立ち上げ、ブラウザからアクセスしてみる。

```sh
$ docker run -d -p 8090:80 httpd_test:0.0.1
12016ba56ed4bce18cafc58ed7169ae2c9df1c6e769619a04591d659b16de78e
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
12016ba56ed4        httpd_test:0.0.1    "httpd-foreground"   4 seconds ago       Up 4 seconds        0.0.0.0:8090->80/tcp   inspiring_nightingale
bb1caef82319        httpd_test:0.0.2    "httpd-foreground"   2 minutes ago       Up 2 minutes        0.0.0.0:8080->80/tcp   adoring_tharp
```

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-commit-container/06_0.0.1.png %}{% endcapture %}
{% capture caption %}
前の状態が残っている
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

大体つかめてきた気がする。