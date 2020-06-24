---
layout : post
title  : "Dockerに慣れる : コンテナを操作する"
date   : 2020/06/24
lastchange : 2020-06-24 13:51:12
tags   :
  - docker
  - httpd
  - container
  - pull
---

## Docker 使ってみたがイマイチわかっていない

[Docker で PHP 環境を作成する]({{site.github.url}}{% link _docs/Docker/docker-php.md %}) で
ひとまず Docker なるものを使ってみたが、まだしっくりこないのでよさそうなまとめを実際に試しながら Docker に慣れようと思う。

参考:

* [【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
* [【Docker】第7回　マウントについて（データボリュームコンテナ）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://webcache.googleusercontent.com/search?q=cache:W9AeAzEC3BsJ:https://www.public.ne.jp/2019/03/08/docker-7/+&cd=1&hl=ja&ct=clnk&gl=jp&client=firefox-b-d "【Docker】第7回　マウントについて（データボリュームコンテナ）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
* [Dockerコンテナの作成、起動〜停止まで - Qiita](https://qiita.com/kooohei/items/0e788a2ce8c30f9dba53 "Dockerコンテナの作成、起動〜停止まで - Qiita")
* [Dockerコンテナの起動と廃棄 \| Think IT（シンクイット）](https://thinkit.co.jp/story/2015/09/08/6383 "Dockerコンテナの起動と廃棄 \| Think IT（シンクイット）")




## 元となる Docker のコンテナを Docker Hub から取得する

{% capture text %}

Docker Hubは、Docker社が営利目的で開発・運営しているサービスです。
自分で作成したコンテナをアップロードして公開・共有することができます。

___

Docker HubにはCanonical、Oracle、Red Hat などのベンダやコントリビュータ（貢献者）によって公開され、
Docker 社によって認証を受けている、多くの 「公式リポジトリ」 があります。ユーザーはこれらを自由に使用することができます。
（コンテナイメージの検索・取得だけであれば、Docker Hubアカウントを作成しなくても利用できます。）

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ざっくり

* Docker 用のコンテナがまとまっているポータル
* 公式が用意した信頼できるコンテナが存在する
* 登録するとリポジトリの公開ができる
* 登録せずともコンテナの取得、ダウンロードはできる



## リポジトリとコンテナ

{% capture text %}

コンテナは「リポジトリ」という単位で管理され、タグを使ったバージョン管理も可能です。
無制限に作成できる公開リポジトリ（パブリックリポジトリ）のほか、一般公開されないプライベートリポジトリも作成できます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ざっくり

* 各々のリポジトリにコンテナを公開して共有できる
* 公開したコンテナ毎にタグを使ったバージョン管理などができる
  * コンテナA
    : Tag
      : 0.0.3
      : 0.0.2
      : 0.0.1
  * コンテナB
    : Tag
      : 1.0.0 pre
      : 0.8.2
      : 0.8.1

## コンテナを検索する

{% capture text %}

まずは、httpdに関するコンテナがどれだけ公開されているのか、検索してみましょう。searchコマンドを実行します。

```sh
$ docker search httpd
```

※limitオプションを指定しない場合、25件が上限となります。
※公式イメージのみを検索したい場合は、以下の様にfilterオプションを指定します。

```sh
$ docker search --filter "is-official=true" httpd
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


やってみる。

```
 $ docker search httpd
NAME                                    DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
httpd                                   The Apache HTTP Server Project                  3073                [OK]                
centos/httpd-24-centos7                 Platform for running Apache httpd 2.4 or bui…   33                                      
centos/httpd                                                                            29                                      [OK]
arm32v7/httpd                           The Apache HTTP Server Project                  9                                       
salim1983hoop/httpd24                   Dockerfile running apache config                2                                       [OK]
clearlinux/httpd                        httpd HyperText Transfer Protocol (HTTP) ser…   1                                       
publici/httpd                           httpd:latest                                    1                                       [OK]
solsson/httpd-openidc                   mod_auth_openidc on official httpd image, ve…   1                                       [OK]
jonathanheilmann/httpd-alpine-rewrite   httpd:alpine with enabled mod_rewrite           1                                       [OK]
lead4good/httpd-fpm                     httpd server which connects via fcgi proxy h…   1                                       [OK]
dariko/httpd-rproxy-ldap                Apache httpd reverse proxy with LDAP authent…   1                                       [OK]
dockerpinata/httpd                                                                      0                                       
e2eteam/httpd                                                                           0                                       
hypoport/httpd-cgi                      httpd-cgi                                       0                                       [OK]
interlutions/httpd                      httpd docker image with debian-based config …   0                                       [OK]
appertly/httpd                          Customized Apache HTTPD that uses a PHP-FPM …   0                                       [OK]
izdock/httpd                            Production ready Apache HTTPD Web Server + m…   0                                       
manasip/httpd                                                                           0                                       
amd64/httpd                             The Apache HTTP Server Project                  0                                       
trollin/httpd                                                                           0                                       
manageiq/httpd_configmap_generator      Httpd Configmap Generator                       0                                       [OK]
itsziget/httpd24                        Extended HTTPD Docker image based on the off…   0                                       [OK]
alvistack/httpd                         Docker Image Packaging for Apache               0                                       [OK]
manageiq/httpd                          Container with httpd, built on CentOS for Ma…   0                                       [OK]
ppc64le/httpd                           The Apache HTTP Server Project                  0                                       
```

スター順、更新があった順にならぶような感じがする。要検証。

公式イメージだけ検索する。

```sh
$ docker search --filter "is-official=true" httpd
NAME                DESCRIPTION                      STARS               OFFICIAL            AUTOMATED
httpd               The Apache HTTP Server Project   3073                [OK]                
```

1 つだけになった。 nginx とかも検索してみる。

```sh
 $ docker search --filter "is-official=true" nginx
NAME                DESCRIPTION                STARS               OFFICIAL            AUTOMATED
nginx               Official build of Nginx.   13374               [OK]                
```

## コンテナを取得する

{% capture text %}

公式イメージの「httpd」があるので、それをpullコマンドでダウンロードしましょう。
※バージョンを指定しない場合、最新版を取得します。

```sh
$ docker pull httpd
```

```
Using default tag: latest
latest: Pulling from library/httpd
…
Status: Downloaded newer image for httpd:latest
```

最新版が取得できました。ではimagesコマンドで現在自分が保有しているイメージのリストを
確認してみましょう。

```sh
$ docker images
```

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              2a51bb06dc8b        2 weeks ago         132MB
hello-world         latest              4ab4c602aa5e        2 months ago        1.84kB
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

```sh
$ docker pull httpd
Using default tag: latest
latest: Pulling from library/httpd
8559a31e96f4: Pull complete 
bd517d441028: Pull complete 
f67007e59c3c: Pull complete 
83c578481926: Pull complete 
f3cbcb88690d: Pull complete 
Digest: sha256:387f896f9b6867c7fa543f7d1a686b0ebe777ed13f6f11efc8b94bec743a1e51
Status: Downloaded newer image for httpd:latest
docker.io/library/httpd:latest
```

httpd の他、依存するものもいくつかダウンロードされているようだ。

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

httpd のイメージが生成されたことも確認できた。


## コンテナを実行する

{% capture text %}

実際にコンテナイメージを起動してみます。

```sh
$ docker run -d -p 8080:80 httpd
```

コマンドが通ったら、ブラウザから「 <http://localhost:8080> 」へアクセスしてみましょう。「It Works!」画面が表示されれば成功です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

```sh
$ docker run -d -p 8080:80 httpd
789ab7aaf815af41ff511e616b64e698f0fd50d90c7f7a87a5e813a316d4e85c
```

ブラウザからアクセスする。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-container/01_httpd.png %}{% endcapture %}
{% capture caption %}
動作確認できた
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

大丈夫そうだ。

nginx を <http://localhost:8090> で動作させてみる。

```sh
 $ docker run -d -p 8090:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
8559a31e96f4: Already exists 
8d69e59170f7: Pull complete 
3f9f1ec1d262: Pull complete 
d1f5ff4f210d: Pull complete 
1e22bfa8652e: Pull complete 
Digest: sha256:21f32f6c08406306d822a0e6e8b7dc81f53f336570e852e25fbe1e3e3d0d0133
Status: Downloaded newer image for nginx:latest
48e23be10dbbe46b98a77a736a3836ca3365de374c64bc0620aa827b951d4bb3
```

`docker pull` せずに `docker run` をすると、自動で該当するコンテナを取得してきてくれる。

{% capture text %}

DockerリポジトリからイメージをPullする記述を省略していますが、
所有していないイメージをRunしようとした場合、自動的にPull→Runと動いてくれます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第7回　マウントについて（データボリュームコンテナ）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://webcache.googleusercontent.com/search?q=cache:W9AeAzEC3BsJ:https://www.public.ne.jp/2019/03/08/docker-7/+&cd=1&hl=ja&ct=clnk&gl=jp&client=firefox-b-d "【Docker】第7回　マウントについて（データボリュームコンテナ）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ブラウザからアクセスする。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-container/02_nginx.png %}{% endcapture %}
{% capture caption %}
nginx も動作できた。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

同じコンピュータ上に

Apache
: <http://localhost:8080>

nginx
: <http://localhost:8090>

という謎環境ができた。

## 動作中のコンテナを確認する

{% capture text %}

psコマンドで、実行中コンテナの状態を確認できます。

```sh
$ docker ps
```
 
```
CONTAINER ID 　IMAGE 　COMMAND 　　　　　　 CREATED 　　　　　　　STATUS 　　　　　　　　PORTS 　　　　　　　　　　NAMES
7f389a9b265d 　httpd 　"httpd-foreground" About a minute ago  Up About a minute 　0.0.0.0:8080->80/tcp 　xenodochial_hugle
```

httpdコンテナが起動している状態であることが確認できます。
※NAMESは、コンテナ起動時にオプションで指定しない場合、Dockerがランダムな名称を付与します。

___

psコマンドに「-a」オプションをつけると、停止中も含め全てのコンテナが確認できます。

___

今度は「-a」オプションで、停止中のコンテナも含め全て表示してみましょう。

```
CONTAINER ID 　IMAGE 　　　　　COMMAND 　　　　　　　CREATED         STATUS                    PORTS  NAMES
7f389a9b265d 　httpd 　　　　　"httpd-foreground"  26 minutes ago  Exited (0) 5 minutes ago         xenodochial_hugle
fd861c5cb6e5 　hello-world 　"/hello" 　　　　　　　2 weeks ago     Exited (0) 2 weeks ago           nervous_kalam
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ざっくり

* `docker ps` で動作中のコンテナを表示する
* `docker ps -a` で停止中も含め生存しているコンテナのプロセス一覧を表示する

`docker ps` をやってみる。

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
48e23be10dbb        nginx               "/docker-entrypoint.…"   28 minutes ago      Up 28 minutes       0.0.0.0:8090->80/tcp   busy_varahamihira
789ab7aaf815        httpd               "httpd-foreground"       32 minutes ago      Up 32 minutes       0.0.0.0:8080->80/tcp   inspiring_mendel
```
Apache と nginx が表示された。


## コンテナの動作を停止する

{% capture text %}

stopコマンドでコンテナIDを指定し、コンテナを停止してみます。

```
$ docker stop 7f389a9b265d
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

## 5.コンテナの停止

起動と同様に、 `docker stop <CONTAINER IDまたはNAME>` で停止することができます。

```sh
$ docker stop 0b5aad08487b
0b5aad08487b
```

または、

```sh
$ docker stop big_hawking
big_hawking
```

STATUSがExitedとなり、停止しているのが確認できます。

```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
0b5aad08487b        ubuntu              "/bin/bash"         38 minutes ago      Exited (0) 9 seconds ago                       big_hawking
```

また、 `docker start コマンドで起動させた場合でも、 `docker attach` 後に `exit`
コマンドで抜けると停止します。

```sh
$ docker attach big_hawking
root@0b5aad08487b:/# 
```

```sh
root@0b5aad08487b:/# exit
exit
```

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
0b5aad08487b        ubuntu              "/bin/bash"         42 minutes ago      Exited (0) 4 seconds ago                       big_hawking
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Dockerコンテナの作成、起動〜停止まで - Qiita](https://qiita.com/kooohei/items/0e788a2ce8c30f9dba53 "Dockerコンテナの作成、起動〜停止まで - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ざっくり

* `docker stop <ID>` で停止できる
* 名前がついていれば `docker stop <Name>` でも停止できる

やってみる。

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
48e23be10dbb        nginx               "/docker-entrypoint.…"   28 minutes ago      Up 28 minutes       0.0.0.0:8090->80/tcp   busy_varahamihira
789ab7aaf815        httpd               "httpd-foreground"       32 minutes ago      Up 32 minutes       0.0.0.0:8080->80/tcp   inspiring_mendel
```

```sh
$ docker stop 48e23be10dbb 789ab7aaf815
48e23be10dbb
789ab7aaf815
```

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
48e23be10dbb        nginx               "/docker-entrypoint.…"   38 minutes ago      Exited (0) 9 seconds ago                       busy_varahamihira
789ab7aaf815        httpd               "httpd-foreground"       43 minutes ago      Exited (0) 8 seconds ago                       inspiring_mendel
```

`<ID>` や `<Name>` は複数を指定することができるようだ。

停止するともちろんアクセスできない。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-container/03_exit.png %}{% endcapture %}
{% capture caption %}
接続不可能になる。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

他に、

{% capture text %}

動作中のコンテナを強制終了したい場合は、killコマンドを実行します。実行するとコンテナは「Exited」になります。

```sh 
$ docker kill 7f389a9b265d
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということもできるようだ。


## コンテナプロセスの消去

{% capture text %}

停止中のコンテナを削除する場合は、rmコマンドを実行します。

```
$ docker rm 7f389a9b265d
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2018/12/14/docker-2/ "【Docker】第2回　コンテナ上でhttpdを動かしてみる/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## コンテナの破棄

起動しているかどうかにかかわらず、コンテナを廃棄するには、
dockerコマンドにrmを付与し、コンテナIDまたはコンテナ名を指定します。
稼働中のコンテナに対して、docker rmに-fオプションを付与しないで実行すると、エラーになり、コンテナは破棄されません。
もし、稼働中のコンテナを強制的に破棄する場合は、-fオプションを付与します。

```sh
# docker rm test0002
Error response from daemon: Conflict, You cannot remove a running container. Stop the container before attempting removal or use -f
FATA[0000] Error: failed to remove one or more containers
 
# docker rm -f test0002
test0002
#
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Dockerコンテナの起動と廃棄 \| Think IT（シンクイット）](https://thinkit.co.jp/story/2015/09/08/6383 "Dockerコンテナの起動と廃棄 \| Think IT（シンクイット）")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

Docker のコンテナは停止させてもプロセスは残り続けるので、どっかのタイミングでプロセスを消去する必要がありそうだ。
消去には `docker rm` コマンドを利用するらしい。やってみる。

```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
48e23be10dbb        nginx               "/docker-entrypoint.…"   38 minutes ago      Exited (0) 9 seconds ago                       busy_varahamihira
789ab7aaf815        httpd               "httpd-foreground"       43 minutes ago      Exited (0) 8 seconds ago                       inspiring_mendel
```

```sh
$ docker rm 48e23be10dbb 789ab7aaf815
48e23be10dbb
789ab7aaf815
```

```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

プロセスが完全に消えたことがわかる。