---
layout : post
title  : "Dockerに慣れる : Dockerfile について"
date   : 2020/06/26
lastchange : 2020-06-26 23:48:47
tags   :
  - docker
  - dockerfile
---

## 設定ファイルでコンテナの状態を残す

[Dockerに慣れる : 変更したコンテナイメージを保存する]({{site.github.url}}{% link _docs/Docker/basic-commit-container.md %})
では変更毎にコンテナイメージを生成することで変更内容を保存していたが、ほかにスマートな方法がいくつかあるらしい。
その内で今回は _Dockerfile_ からコンテナイメージを生成する方法を見てみる。

参考:

* [【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/25/docker-4/ "【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")


## _Dockerfile_ とは

{% capture text %}

Dockerfileとは、Dockerイメージをビルドする指示を記載したプレーンテキストファイルです。開発環境におけるMakefileのように、Dockerで定義されている書式に従って記述します。

ファイルには、ベースとするイメージの取得やコンテナ内部で処理を行うコマンド等を記載していきます。

___

Dockerfileを使用するメリットとして、以下が挙げられます。

* ファイルを配布することで同一の環境を複製できる。常に同じ構成で環境構築することができる。
* ファイルの記述を読むことで、アプリケーション構成や構築手順などの情報を確認することができる。
* ビルドコマンド１つでコンテナが生成できるため、構築作業の省力化や作業ミスの低減にも役立つ。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/25/docker-4/ "【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

[Dockerに慣れる : 変更したコンテナイメージを保存する]({{site.github.url}}{% link _docs/Docker/basic-commit-container.md %})
で内部に入って変更していたことを _Dockerfile_ で自動化できるらしい。
同時に、他人に _Dockerfile_ を渡せば同様の環境をすぐに構築できるらしい。



## _Dockerfile_ を作成する

{% capture text %}

まず任意の場所（今回はユーザーディレクトリ直下に「docker」ディレクトリを用意しました）に、
「Dockerfile_httpd_build」という名前でファイルを作成します。

```sh
$ touch Dockerfile_httpd_build
```

そして、以下の様にファイルを編集してみます。
 
```dockerfile
#--------------------------------------------------------
# ベースイメージを指定
#--------------------------------------------------------
FROM httpd:latest
#--------------------------------------------------------
# イメージの作成者
#--------------------------------------------------------
MAINTAINER PublicRelations
#--------------------------------------------------------
# 環境変数
#--------------------------------------------------------
ENV DIRPATH /usr/local/apache2
#--------------------------------------------------------
# ワークディレクトリ指定
#--------------------------------------------------------
WORKDIR $DIRPATH/htdocs
#--------------------------------------------------------
# コマンド実行
#--------------------------------------------------------
RUN echo "Hello Dockerfile!" > index.html
#--------------------------------------------------------
# 特定のネットワーク・ポートをコンテナ実行時にリッスンさせる
#--------------------------------------------------------
EXPOSE 80
```

`FROM`
: ベースとなるイメージを指定します。一番始めに書かれていなくてはなりません。

`MAINTAINER`
: 生成するイメージの製作者（Author）を指定します。

`ENV`
: 環境変数を「<変数名> ＜値＞」で指定します。

`WORKDIR`
: 作業ディレクトリを指定します。

`RUN`
: シェル形式のコマンドを実行します。

`EXPOSE`
: コンテナが接続用にリッスンするポートを指定します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/01/25/docker-4/ "【Docker】第4回　Dockerfileを使ってイメージをビルドする/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。
適当なディレクトリを作成し、以下に _Dockerfile_ を作成する。

```sh
$ mkdir test
$ cd test
$ vimr Dockerfile
```

以下のように _Dockerfile_ を作成する。

```dockerfile
FROM httpd:latest

MAINTAINER dummy

ENV DIRPATH /usr/local/apache2

WORKDIR $DIRPATH/htdocs

RUN echo "Hello Dockerfile!" > index.html

EXPOSE 80
```

コンテナイメージをビルドしてみる。

```sh
$ docker build -t httpd_build .
Sending build context to Docker daemon  2.048kB
Step 1/6 : FROM httpd:latest
latest: Pulling from library/httpd
8559a31e96f4: Pull complete 
bd517d441028: Pull complete 
f67007e59c3c: Pull complete 
83c578481926: Pull complete 
f3cbcb88690d: Pull complete 
Digest: sha256:387f896f9b6867c7fa543f7d1a686b0ebe777ed13f6f11efc8b94bec743a1e51
Status: Downloaded newer image for httpd:latest
 ---> ccbcea8a6757
Step 2/6 : MAINTAINER dummy
 ---> Running in 96183640bed4
Removing intermediate container 96183640bed4
 ---> 33aa3e26cb4e
Step 3/6 : ENV DIRPATH /usr/local/apache2
 ---> Running in a5cc85ee0490
Removing intermediate container a5cc85ee0490
 ---> 1dd11a332f5e
Step 4/6 : WORKDIR $DIRPATH/htdocs
 ---> Running in 0b6bdf1a69d4
Removing intermediate container 0b6bdf1a69d4
 ---> 2a34893eedb0
Step 5/6 : RUN echo "Hello Dockerfile!" > index.html
 ---> Running in a4a269148851
Removing intermediate container a4a269148851
 ---> 62ee492879cf
Step 6/6 : EXPOSE 80
 ---> Running in 19421a303205
Removing intermediate container 19421a303205
 ---> 204640489621
Successfully built 204640489621
Successfully tagged httpd_build:latest
```

できたらしいのでコンテナイメージを確認する。

```sh
 $ docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
httpd_build                            latest              204640489621        6 seconds ago       166MB
dotnetsdk3.1alpine3.12                 latest              25db00bce912        7 hours ago         422MB
mcr.microsoft.com/dotnet/core/aspnet   3.1-alpine3.12      4213a3f7e30c        8 days ago          105MB
httpd                                  latest              ccbcea8a6757        2 weeks ago         166MB
```

`httpd_build` イメージができている。動作させてみる

```sh
$ docker run -d -p 8080:80 httpd_build
96f8aede5a011f2624a40a44c17287154e52c7a4a6a0b8c2781f3641c3acca3f
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
96f8aede5a01        httpd_build         "httpd-foreground"   6 seconds ago       Up 5 seconds        0.0.0.0:8080->80/tcp   brave_kowalevski
```

ブラウザで動作を確認する

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-dockerfile/01_hello_dockerfile.png %}{% endcapture %}
{% capture caption %}
_Dockerfile_ が変更できたようだ
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}
