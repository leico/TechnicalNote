---
layout : post
title  : Docker で PHP 環境を作成する
date   : 2020/06/17
lastchange : 2020-06-20 23:18:30
tags   :
  - docker
  - nginx
  - phpmyadmin
  - mysql
---

## Docker を使う時がきた

で、使ってみたらなるほど便利だとなった。とりあえず PHP 環境が必要だったのでその構築を行った。

参考:
* [いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1 "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
* [コンテナセキュリティのリスクと対策 - sagantaf](http://sagantaf.hatenablog.com/entry/2019/07/30/141016 "コンテナセキュリティのリスクと対策 - sagantaf")
* [DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita](https://qiita.com/nemui_/items/f911be7ffa4f29293fd5 "DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita")
* [docker-compose.ymlの書き方について解説してみた - Qiita](https://qiita.com/yuta-ushijima/items/d3d98177e1b28f736f04 "docker-compose.ymlの書き方について解説してみた - Qiita")
* [Compose file version 3 reference | Docker Documentation](https://docs.docker.com/compose/compose-file/#depends_on "Compose file version 3 reference | Docker Documentation")



## Docker とは

{% capture text %}

## ホスト型仮想化

ホストOS上で動作する仮想化ソフトウェアを利用してVMを管理する方式。
オーバーヘッドが大きい。
代表例はVirtualBox、VMWare Player。

![image.png](https://qiita-image-store.s3.amazonaws.com/0/221948/8e77ad87-b140-173d-554d-0db84161bda2.png)


## ハイパーバイザ型仮想化

ハードウェア上のハイパーバイザを利用してVMを管理する方式。
ホストOSなしでハードウェアを直接制御する。
オーバーヘッド大きい。
代表例はKVM、Heper-V、ESXi、Xen。

![image.png](https://qiita-image-store.s3.amazonaws.com/0/221948/f011b839-94bb-86aa-0309-3a2759a6749b.png)

## コンテナ型仮想化

ホストOS上でコンテナと呼ばれるVMと似たようなふるまいをする仮想的な区画を用意して管理する方式。
コンテナはホストOSのカーネルを利用する。
通常、1コンテナ1プロセスで使用するためWebサーバコンテナとかDBサーバコンテナとかログ管理コンテナとか複数のコンテナを立てる構成となる。

![image.png](https://qiita-image-store.s3.amazonaws.com/0/221948/2b62e1e8-8e7b-7959-6b39-d91c37373cbc.png)

### 他の仮想化と比べたコンテナ型仮想化(Docker)のメリット

* 処理が速い
  * VM毎にCPUやメモリやストレージなどを割り当てるような処理が必要がなくオーバヘッドが少ないから
  * ゲストOSが無いため消費するリソースが少ないから
  * ゲストOSが無いためアプリケーションまでのパケット転送処理(コンテキストスイッチなど)が少ないから
* 起動が速い
  * ゲストOSを立ち上げるわけではないから
* 可搬性が高い
  * Dockerエンジンさえあれば基本どこでも動く。他の仮想化は足回りとかもうちょっと苦労する。
  * Dockerfileやdocker-compose.ymlによるインフラのコード化
* イメージが軽い
  * Dockerイメージはレイヤで管理しているから
* Dockerイメージを簡単に共有できる仕組みが整っている
  * 特に、公式Dockerイメージが用意されている場合(JenkinsやWordPressなど)はデプロイがめちゃ楽
  * ECRなどプロジェクトに閉じているかつフルマネージドな環境が整っている


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1 "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記のまとめがわかりやすかった。 1つの OS 内で、サービス毎にアクセスできる範囲をコンテナ内に限定することで
他のサービスと切り分けて管理ができる。

一方、他の仮想化とは異なりコンテナのサービスは全て同一の OS を共有することになる。

{% capture text %}

**ホストOS**

コンテナはホストとカーネルを共有しているため、ホスト側のセキュリティ設定をしっかり実施しておく必要があります。

ホストOS自身への対策としては、定期的なOSアップデート確認および適用は必須です。

また、下記のようなホストOSのセキュリティ機能を利用して、コンテナプロセスの動きを制限することも可能です。

* SELinuxでMAC（強制アクセス制御）を使い、他の空間のリソースは使えないようにする
* Cgoupsを使ってプロセスのリソース使用量（CPU、メモリ、ディスクIO、ネットワークなど）を制限し、
  １つのコンテナにDoS攻撃を受けてリソースが圧迫されても、同じホスト上の別のコンテナがリソース不足になることを防ぐ
* Capabilityにて一部のroot権限が必要な機能のみをコンテナプロセスに与える

別の観点として、ホストOSからのコンテナの設定として下記の対策も有効です。

* コンテナに特化したホストOSを利用する
* コンテナが呼べるカーネルコールを制限する
* DockerやKubernetesのAPIコマンドの範囲をローカルネットワークのみなどに制限する

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[コンテナセキュリティのリスクと対策 - sagantaf](http://sagantaf.hatenablog.com/entry/2019/07/30/141016 "コンテナセキュリティのリスクと対策 - sagantaf")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記のページにセキュリティ関係で考えなければならない項目が挙げられていた。


## Docker で PHP サーバサイドのテスト環境を構築する

普通にサーバ立てる程度に考えなければならないことは山ほどありそうだが、とりあえず PHP が動作するところまで構築してみる。



### docker-compose

{% capture text %}

* `docker-compose.yml` ファイルに記載してまとめてインストールする方法

筆者は `docker-compose.yml` ファイルでまとめてインストールする方が楽で混乱しないと思ったので、この手法を使いたいと思います。


## Docker Compose

`docker-compose.yml` ファイルでまとめてインストールする際に、 **Docker Compose** と呼ばれるもので一括管理します。
（上の図のタコがDocker Composeのイメージになります）

Docker Composeは、複数のDockerコンテナを自動で立ち上げる構成管理ツールになります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita](https://qiita.com/nemui_/items/f911be7ffa4f29293fd5 "DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

# compose

同一ホストOS上の複数コンテナを一括管理するコンポーネント。
"docker-compose.yml"ファイルに複数コンテナの構成情報を定義し、docker-composeコマンドで起動や停止などの管理をする。
通常、複数のサーバ群(コンテナ群)で1つのサービスを構成するため、それらをまとめるcomposeの需要がある。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1 "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


複数のコンテナをまとめて制御するためのコマンドで、 _docker-compose.yml_  というファイルによって制御するらしい。


{% capture text %}

次に、土台となるdocker-compse.ymlを作成します。
Docker Composeは docker-compose.ymlファイルに書かれた内容に従ってコンテナーを管理しています。

```yml
version: '3'
services:
  nginx:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./www/html:/var/www/html
    depends_on:
      - php

  php:
    build: ./php
    volumes:
      - ./www/html:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:5.7
    ports:
      - 13306:3306
    volumes:
      - ./mysql/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    ports:
      - 8888:80
    depends_on:
      - db
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita](https://qiita.com/nemui_/items/f911be7ffa4f29293fd5 "DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

少し内容について調べる。

{% capture text %}

### versionって?

docker-composeで使用するバージョンを定義しています。

公式サイトによると、この記事を執筆時点(２０１８年10月13日)では、3系が最新バージョンとなっていますね。

<https://docs.docker.com/compose/compose-file/>

このバージョンによって、ComposeFileの書き方が異なるので、注意が必要。

ちなみに、CircleCIというCIツールは、ComposeFileのバージョンが2系以上じゃないとWarningがでます。
(将来的に1系が使われなくなる予定のため)


### Services(サービス)ってなーに？

Docker-Composeでは、アプリケーションを動かすための各要素を **Service** と読んでいます。

そのため、ComposeFileにも、serviceとして、それぞれのServicesの内容をネストさせて記述していきます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[docker-compose.ymlの書き方について解説してみた - Qiita](https://qiita.com/yuta-ushijima/items/d3d98177e1b28f736f04 "docker-compose.ymlの書き方について解説してみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

#### image

ベースイメージの指定をする。
Dockerイメージを直接指定する方法。
docker-compose.ymlにはimageかbuildのどちらかの記載が必須。(当たり前な話だけどコンテナ起動にはイメージ必須だから)

```yml
sampleserver:
  image: ubuntu
```

#### build

Dockerfileの指定をする。
Dockerfile内のFROM命令でイメージ指定できるため、間接的にイメージを指定できる。
docker-compose.ymlにはimageかbuildのどちらかの記載が必須。(当たり前な話だけどコンテナ起動にはイメージ必須だから)

* ディレクトリ構成
    ```bash
    # ls
    Dockerfile  docker-compose.yml
    ```
* Dockerfile
    ```
    FROM ubuntu
    ```
* docker-compose.yml
    ```yml
    sampleserver:
      build .
    ```

#### command

コンテナ内で実行するコマンドの指定をする。

```yml
command: /bin/bash
```

#### links

コンテナ間のネットワーク設定をする。
linksで指定したコンテナへ、指定元コンテナから接続可能にする。
コンテナ内の/etc/hostsに名前設定がされる。

```yml
links:
  - dbserver
```

#### ports

コンテナの外部への公開ポート設定をする。
以下はホストOSへの5000番ポートアクセスをコンテナの5000ポートに転送する設定例。

```yml
ports:
  - "5000:5000"
```

#### expose

コンテナ間のみでの公開ポート設定をする。

```yml
expose:
  - "5000"
```

#### volumes

コンテナにボリュームをマウントする。
\< マウント元のパス \> : \< マウント先（コンテナ）のパス \> の形式で指定する。(コンテナ側は省略可)

```yml
volumes:
  - /tmp:/tmp
```

#### volumes_from

別のコンテナからボリュームをマウントする。
下はstorageという名前のコンテナからマウントする例。

```yml
volumes_from:
 - storage
```


#### environment

コンテナ内の環境変数設定をする。

```yml
environment:
  - hoge=hogehoge
```

#### env_file

コンテナ内の環境変数をファイル読み込みで設定する。

* envfile
    ```env
    hoge=hogehoge
    fuga=fugafuga
    ```
* docker-compose.yml
    ```yml
    env_file: envfile
    ```

#### container_name

コンテナの名前設定をする。

```yml
container_name: sample-container1
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1 "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

### depends_on

Express dependency between services. Service dependencies cause the following behaviors:

* `docker-compose up` starts services in dependency order. In the following example, `db` and `redis` are started before `web` .
* `docker-compose up SERVICE` automatically includes `SERVICE` ’s dependencies. In the example below,
  `docker-compose up web` also creates and starts `db` and `redis` .
* `docker-compose stop` stops services in dependency order. In the following example, `web` is stopped before `db` and `redis` .

Simple example:

```yml
version: "3.8"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Compose file version 3 reference | Docker Documentation](https://docs.docker.com/compose/compose-file/#depends_on "Compose file version 3 reference | Docker Documentation")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

今回の _docker-compose.yml_ を `services` 毎に見てみる。

#### nginx 
```yml
  nginx:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./www/html:/var/www/html
    depends_on:
      - php
```

`image`
: `nginx` 最新版

`ports`
: ホストの `8080` をコンテナの `80` に転送

`volumes`
: ホストの _./nginx/nginx.conf_ を コンテナの _/etc/nginx/conf.d/default.d_ に割り当てる
: ホストの _./www/html_ をコンテナの _/var/www/html_ に割り当てる

`depends_on`
: `php` サービスを先に起動する


#### php

```yml
  php:
    build: ./php
    volumes:
      - ./www/html:/var/www/html
    depends_on:
      - db
```

`build`
: ホストの _./php_ ディレクトリ内の _Dockerfile_ を利用してイメージを構築する

`volumes`
: ホストの _./www/html_ を _/var/www/html_ に割り当てる

`depends_on`
: `db` サービスを先に起動する


#### db

```yml
  db:
    image: mysql:5.7
    ports:
      - 13306:3306
    volumes:
      - ./mysql/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret

```

`image`
: mysql ver.5.7 を利用する

`ports`
: ホストの `13306` をコンテナの `3306` に割り当てる

`volumes`
: ホストの _./mysql/data_ をコンテナの _/var/lib/mysql_ に割り当てる

`environment`
: コンテナ内の環境変数 `MYSQL_ROOT_PASSWORD` を `secret` にする


#### phpmyadmin

```yml
 phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    ports:
      - 8888:80
    depends_on:
      - db
```

`image`
: `phpmyadmin` 最新版

`ports`
: ホストの `8888` をコンテナの `80` に割り当てる

`depends_on`
: `db` サービスを先に起動する

### Dockerfile

{% capture text %}

## php/Dockerfile

```docker
FROM php:7.2-fpm
COPY php.ini /usr/local/etc/php/
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita](https://qiita.com/nemui_/items/f911be7ffa4f29293fd5 "DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

## Dockerfile概要

Dockerfileはコンテナの構成情報を定義したファイルである。
Dockerfileを用意すれば何回もDockerコマンドを叩く必要なく、1コマンドでDockerイメージを生成できるため、構築の手間を省力化したりミスをなくしたりDockerfileそのものを手順書の一部として扱える。逆にDockerfileが無いとコンテナ操作に関する手順書やパラメータシートなどを別途管理する必要が出てきてしまう。
新イメージを作るのにベースイメージからいったんコンテナを起動して、命令実行している。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1#dockerfile "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

コンテナをデフォルトの状態からカスタムして実行する場合に _Dockerfile_ を利用して `build` するのが有効なようだ。
_Dockerfile_ の書き方もかいてあった。

{% capture text %}

### FROM

ベースイメージの指定をする命令。

```docker
FROM centos
```

### MAINTAINER

Dockerfileの作成者情報を設定する命令。
ただし、MAINTAINERは1.13から非推奨であり、代わりにLABEL命令を使います。

```docker
MAINTAINER goldkou
```

### ENV

コンテナ内の環境変数を設定する命令。
runコマンドのenvオプションと同様のことができるが、envオプションの内容が優先となる。

```docker
ENV hoge=hogehoge\
    fuga=fugafuga
```

### WORKDIR

場所(ディレクトリ)を移動する命令。
次以降の命令は移動した先の場所が起点となる。

```docker
WORKDIR /tmp
RUN ["pwd"]
```

### USER

ユーザ変更する命令。
次以降の命令者は変更した後のユーザになる。

```docker
USER admin
RUN ["whoami"]
```

### LABEL

イメージにメタ情報(バージョンやコメントなど)を設定する命令。
inspectコマンドで内容を確認できる。

```docker
LABEL title="sampleImage"\
      version="1.0"\
      description="This is a sample."
```

```sh
# docker image inspect 1a5099f89ade |grep title
                "title": "sampleImage",

# docker image inspect 1a5099f89ade |grep version
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180531",
                "version": "1.0"

# docker image inspect 1a5099f89ade |grep description
                "description": "This is a sample.",
```

### EXPOSE

コンテナの公開ポート番号を設定する命令。
外部からアクセスする場合にはrunコマンドの-pオプションによるポート転送が必要。

```docker
EXPOSE 80
```

### ADD

ファイルやディレクトリを取得する命令。
ホストOS上のローカルファイルでもリモートファイルでも可。
リモートファイルの場合はパーミッションが600になる点に注意。

```docker
ADD https://github.com/docker/cli/blob/master/README.md /tmp
```

### COPY

ファイルやディレクトリを取得する命令。
ホストOS上のローカルファイルのみ可。ローカルファイルのコピーを行う場合はCOPYを使うことが推奨されている。

```docker
COPY tmp.txt /tmp
```

### VOLUME

ボリュームを割りあてる命令。
コンテナ内のマウントポイントを指定する。
ホストOSなどからボリュームがマウントされる。具体的にどこにマウントされるかなどの詳細はこちらの記事が勉強になりました。
コンテナ内のファイルはコンテナを削除すると消えてしまうので、重要なデータはコンテナでなく外部ボリュームで管理すべし。

```docker
VOLUME /myvol
```

### ONBUILD

次のbuildで実行するコマンドをイメージに仕込む命令。
自身のDockerfileから生成したDockerイメージをベースイメージとして別のDockerfileでビルドされるときに実行されるコマンドを設定する。
（正直なところ、ONBUIDならではの効果的な使い道がまだ分からない。ADDやCMDとかでそれできるよねって感じ。ベースイメージを軽くできるとか？）
以下は、ADD命令を利用してホストOS上のtmp.tarファイルをコンテナの/var/www/html配下に格納する例。

```docker
ONBUILD ADD tmp.tar /var/www/html/
```

### RUN

Dockerイメージを作成する際にコンテナ内でコマンドを実行する命令。
実行形式はExec形式とShell形式の2種類がある。
Exec形式はシェルを介さずに実行する。公式的にはこちらが推奨とされている。
Shell形式は/bin/sh -cのシェルを介して実行する。

* Exec形式例
  ```docker
  RUN ["yum -y install httpd"]
  ```
* Shell形式例
  ```docker
  RUN yum -y install httpd
  ```

CMDやENTRYPOINTとの違いは、RUNは「Dockerイメージを作成するためのコマンド」を定義するのに対し、
CMDやENTRYPOINTは「Dockerイメージが起動してから実行されるコマンド」を定義する点である。

### CMDとENTRYPOINT

#### CMD

Dockerfileで作成したDockeイメージから稼働したコンテナ内で指定コマンドを実行する命令。
runコマンドの引数に指定したコマンドでCMDの内容をオーバーライドできる。
1つのDockerfileにCMDは1つのみ。
Exec形式はシェルを介さずに実行する。公式的には推奨とされている。
Shell形式は/bin/sh -cのシェルを介して実行する。

* Exec形式例
  ```docker
  CMD ["ping","127.0.0.1","-c","5"]
  ```
* Shell形式例
  ```docker
  CMD ping 127.0.0.1 -c 5
  ```

#### 複数のCMDを実行したい場合

複数CMD書いても最後の行のものしか有効になりません。
複数のコマンドを実行したい場合は、コマンド群をシュルスクリプトに定義し、CMDでそのシェルを実行するようにしましょう。

#### ENTRYPOINT

Dockerfileで作成したDockeイメージから稼働したコンテナ内で指定コマンドを実行する命令。
runコマンドの引数に指定したコマンドでCMDの内容をオーバーライドできるが、--entrypointオプションが必要。
1つのDockerfileにENTRYPOINTは1つのみ。
Exec形式はシェルを介さずに実行する。公式的には推奨とされている。
Shell形式は/bin/sh -cのシェルを介して実行する。公式的には非推奨とされている。

* Exec形式例
  ```docker
  ENTRYPOINT ["ping","127.0.0.1","-c","5"]
  ```
* Shell形式例
  ```docker
  ENTRYPOINT ping 127.0.0.1 -c 5
  ```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1#dockerfile "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

今回は PHP のコンテナをカスタムしている。短い内容を見てみる。

```docker
FROM php:7.2-fpm
COPY php.ini /usr/local/etc/php/
```

`FROM`
: php 7.2 fpm コンテナをベースにする

`COPY`
: _php.ini_ をコンテナ内の _/usr/local/etc/php/_ にコピーする

## docker-compose で全てを立ち上げる

他、各種設定ファイルやソースファイルの記述が間違っていなければ問題なく動作するはず。
動作させるには `docker-compose` コマンドを利用する。

{% capture text %}
## コンテナの起動

1. 全てのファイルが用意できたら、 `cdコマンド` で作業ディレクトリに移動します。
2. `$ docker-compose up -d` を入力するとPHP、MySQL、Nginxがインストールされます。
3. `$ docker ps` を入力して、コンテナが立ち上がっているか確認。
  ```sh
  CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS              PORTS                                NAMES
  1c8d70c32262        nginx:latest                     "nginx -g 'daemon of…"   31 minutes ago      Up 31 minutes       0.0.0.0:8080->80/tcp                 phpproject_nginx_1
  c7394112b393        phpmyadmin/phpmyadmin:latest     "/run.sh supervisord…"   31 minutes ago      Up 31 minutes       9000/tcp, 0.0.0.0:8888->80/tcp       phpproject_phpmyadmin_1
  73174d360ed4        phpproject_php                   "docker-php-entrypoi…"   31 minutes ago      Up 31 minutes       9000/tcp                             phpproject_php_1
  ff496b0a3cdd        mysql:5.7                        "docker-entrypoint.s…"   31 minutes ago      Up 31 minutes       33060/tcp, 0.0.0.0:13306->3306/tcp   phpproject_db_1
  e1b4fae8bea7        newdeveloper/apache-php:latest   "/bin/bash /run.sh"      6 hours ago         Up 6 hours          0.0.0.0:32768->80/tcp                apache-php
  ```
4. ローカルホストにアクセスして、phpinfoが表示されていればOKです。

---

`docker-compose up -d` で実行すると、コンテナをバックグラウンドで起動し、実行し続けた状態になります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita](https://qiita.com/nemui_/items/f911be7ffa4f29293fd5 "DockerによるPHP開発環境構築（PHP + MySQL + Nginx） - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

### ps

コンテナの一覧表示をする。

```sh
# docker-compose ps
        Name             Command    State    Ports
--------------------------------------------------
```

### up

docker-compose.ymlからコンテナの生成と起動を行う。

```sh
# cat docker-compose.yml
ubuntuServer:
   image: ubuntu

centosServer:
   image: centos

# docker-compose up
Pulling ubuntuServer (ubuntu:)...
latest: Pulling from library/ubuntu
c64513b74145: Pull complete
01b8b12bad90: Pull complete
c5d85cf7a05f: Pull complete
b6b268720157: Pull complete
e12192999ff1: Pull complete
Digest: sha256:3f119dc0737f57f704ebecac8a6d8477b0f6ca1ca0332c7ee1395ed2c6a82be7
Status: Downloaded newer image for ubuntu:latest
Creating sample_ubuntuServer_1 ... done
Creating sample_centosServer_1 ... done
Attaching to sample_ubuntuServer_1, sample_centosServer_1
sample_ubuntuServer_1 exited with code 0
sample_centosServer_1 exited with code 0

# docker-compose ps
        Name             Command    State    Ports
--------------------------------------------------
sample_centosServer_1   /bin/bash   Exit 0
sample_ubuntuServer_1   /bin/bash   Exit 0
```

### scale

docker-compose.ymlファイルから生成するコンテナの数を指定する。

```sh
# cat docker-compose.yml
ubuntuServer:
   image: ubuntu

centosServer:
   image: centos

# docker-compose scale ubuntuServer=2 centosServer=3
WARNING: The scale command is deprecated. Use the up command with the --scale flag instead.
Creating sample_ubuntuServer_1 ... done
Creating sample_ubuntuServer_2 ... done
Creating sample_centosServer_1 ... done
Creating sample_centosServer_2 ... done
Creating sample_centosServer_3 ... done

# docker-compose ps
        Name             Command    State    Ports
--------------------------------------------------
sample_centosServer_1   /bin/bash   Exit 0
sample_centosServer_2   /bin/bash   Exit 0
sample_centosServer_3   /bin/bash   Exit 0
sample_ubuntuServer_1   /bin/bash   Exit 0
sample_ubuntuServer_2   /bin/bash   Exit 0
```

### run

起動済みコンテナで新たにコマンドを実行する。
以下は/bin/bashを実行する例。

```sh
# docker-compose run ubuntuServer /bin/bash
root@552410439d29:/#
```

### start

複数コンテナの一括起動をする。
何も指定しなければ一括となる。

```sh
# docker-compose start
Starting ubuntuServer ... done
Starting centosServer ... done
```

### stop

複数コンテナの一括停止をする。
何も指定しなければ一括となる。

```sh
# docker-compose stop
```

### restart

複数コンテナの一括再起動をする。
何も指定しなければ一括となる。

```sh
# docker-compose restart
Restarting sample_ubuntuServer_1 ... done
Restarting sample_centosServer_1 ... done
```

### rm

複数コンテナの一括削除をする。
何も指定しなければ一括となる。

```sh
# docker-compose rm
Going to remove sample_ubuntuServer_1, sample_centosServer_1
Are you sure? [yN] y
Removing sample_ubuntuServer_1 ... done
Removing sample_centosServer_1 ... done
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita](https://qiita.com/gold-kou/items/44860fbda1a34a001fc1#dockerfile "いまさらだけどDockerに入門したので分かりやすくまとめてみた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}
