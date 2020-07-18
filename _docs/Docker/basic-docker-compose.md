---
layout : post
title  : "Dockerに慣れる : docker-compose"
date   : 2020/07/17
lastchange : 2020-07-19 01:49:31
tags   :
  - docker
  - compose
  - httpd
---

## Docker Compose を単体のコンテナで使う

[Dockerに慣れる : ボリュームをマウントする]({{site.github.url}}{% link _docs/Docker/basic-bind-mount.md %})
では、ホストのディレクトリをコンテナ側から参照する方法を試した。

今回は Docker Compose を利用してみる。

{% capture text %}

## Docker Composeとは

[Docker Compose](https://docs.docker.com/compose/)
は、複数のコンテナで構成されるアプリケーションについて、Dockerイメージのビルドや各コンテナの起動・停止などをより簡単に行えるようにするツールです。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Docker入門（第六回）〜Docker Compose〜 \| さくらのナレッジ](https://knowledge.sakura.ad.jp/16862/ "Docker入門（第六回）〜Docker Compose〜 \| さくらのナレッジ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

実際には「複数のコンテナを組み合わせて動かす」場合がほとんどだと思います。
その場合、コンテナの起動順序や個別のパラメータ設定、オプションなどの情報を管理していくことが規模に応じて複雑化し、大変になっていきます。

そのような場合、Docker Composeを使用する（＝複数コンテナ同士の依存関係や設定をymlファイルに記述する）ことで、手順の簡略化・手順誤りを軽減することができます（ファイル内容を変更しない限り、何度でも同じ条件で再現が可能です）。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発\｜ITイノベーション/最先端技術\｜パブリックリレーションズ](https://www.public.ne.jp/2019/04/08/docker-8/ "【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発\｜ITイノベーション/最先端技術\｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



と、本来複数のコンテナからなるサービスを制御するためのソフトウェアらしいのだが、
いきなり複数台を制御しようとしても複雑になるので、単体からやることにした。

`docker run` でディレクトリをマウントする時に絶対パスなのが大変なのだが、
Docker Compose だと相対パスで行けそうだった、というのもある。

参考 : 

* [Docker入門（第六回）〜Docker Compose〜 \| さくらのナレッジ](https://knowledge.sakura.ad.jp/16862/ "Docker入門（第六回）〜Docker Compose〜 \| さくらのナレッジ")
* [【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/04/08/docker-8/ "【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ") 
 


## Docker Compose から httpd を立ち上げる

### Docker Compose の確認

{% capture text %}

```sh
$ docker-compose --version
docker-compose version 1.23.1, build b02f1306
```

インストールされていることを確認できたら、ここから実際に定義ファイルを作成していきます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/04/08/docker-8/ "【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

```sh
$ docker-compose --version
docker-compose version 1.25.5, build 8a1c60f6
```

大丈夫そうだ。

### Docker Compose のファイルを作成する

{% capture text %}

まず準備として、作業ディレクトリを用意します。
今回はユーザーディレクトリ直下に _docker_wp_ ディレクトリを作成し、
そこへ _docker-compose.yml_ を作成することとします。

```sh
$ mkdir docker_wp
$ touch docker_wp/docker-compose.yml
$ vi docker_wp/docker-compose.yml
```

ファイルの内容は以下の様にしました。

 
```yml
version: '3'

services:
  wordpress:
    image: wordpress:latest
    depends_on:
      - mysql
    ports:
      - "10080:80"
    env_file: .env

  mysql:
    image: mysql:5.7
    volumes:
      - "./.data/db:/var/lib/mysql"
    env_file: .env
```

記述の内容を簡単に解説しますと

`version`
: 定義ファイルの書式バージョン（現在は3が最新）

`services`
: アプリケーションの要素名

`image`
: ベースとするコンテナイメージの指定

`depends_on`
: コンテナの依存関係の指定（今回の例では「wordpress」サービスは「mysql」サービスに依存します）

`ports`
: 起動ポートの指定

`volumes`
: ボリュームマウントの指定

`env_file`
: 環境変数を定義したファイルの指定

となっています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/04/08/docker-8/ "【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これを `httpd` 単体用にカスタマイズしていく。やりたいことは

1. ベースイメージの指定
2. ポート設定
3. ボリュームマウントの指定

なので、参考サイトからこの辺りの設定を行う。

まずはディレクトリを作って移動する。

```sh
$ cd sandbox/docker_test
$ mkdir docker-compose
$ cd docker-compose 
```

_docker-compose.yml_ を作成する。

```sh
$ vimr docker-compose.yml
```

```yml
version: '3'

services :
  httpd :
    image : httpd:latest
    ports : 
      - "8080:80"
    volumes :
      - "./htdocs:/usr/local/apache2/htdocs"
```

`volumes` で _htdocs_ ディレクトリをマウントする設定をしているのでディレクトリを作成する

```sh
$ mkdir htdocs
```

_htdocs_ 内に _index.html_ を作成する。

```sh
$ vimr htdocs/index.html
```

```html
<!DOCTYPE html>
<html lang="ja" xml:lang="ja">
  <head>
    <meta charset="utf-8">
  </head>
  <body>
    <p>Test page</p>
  </body>
</html>
```

ものが揃ったので Docker Compose で起動してみる。

### Docker Compose で httpd を起動する

{% capture text %}

準備が整ったら、以下のコマンドで起動します。

```sh
$ docker-compose up -d
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/04/08/docker-8/ "【Docker】第8回　Docker Composeで複数のコンテナを連携する/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

```sh
$ docker-compose up
Creating network "docker-compose_default" with the default driver
Pulling httpd (httpd:latest)...
latest: Pulling from library/httpd
8559a31e96f4: Pull complete
bd517d441028: Pull complete
f67007e59c3c: Pull complete
83c578481926: Pull complete
f3cbcb88690d: Pull complete
Digest: sha256:387f896f9b6867c7fa543f7d1a686b0ebe777ed13f6f11efc8b94bec743a1e51
Status: Downloaded newer image for httpd:latest
Creating docker-compose_httpd_1 ... done
Attaching to docker-compose_httpd_1
httpd_1  | AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
httpd_1  | AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
httpd_1  | [Fri Jul 17 09:28:56.502949 2020] [mpm_event:notice] [pid 1:tid 140531077407872] AH00489: Apache/2.4.43 (Unix) configured -- resuming normal operations
httpd_1  | [Fri Jul 17 09:28:56.503106 2020] [core:notice] [pid 1:tid 140531077407872] AH00094: Command line: 'httpd -D FOREGROUND'
httpd_1  | 172.18.0.1 - - [17/Jul/2020:09:29:21 +0000] "GET / HTTP/1.1" 200 141
```

動いたらしい。ブラウザからアクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-docker-compose/01_httpd.png %}{% endcapture %}
{% capture caption %}
動作確認できた
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

あっさりと動いてしまって戸惑っている。

バックグラウンド起動をさせ、 _htdocs/index.html_ を変更してみる。

```sh
$ docker-compose up -d
```

```sh
$ vimr htdocs/index.html
```

```html
<!DOCTYPE html>
<html lang="ja" xml:lang="ja">
  <head>
    <meta charset="utf-8">
  </head>
  <body>
    <p>Test page</p>
    <p>and more</p>
  </body>
</html>
```

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-docker-compose/02_edit.png %}{% endcapture %}
{% capture caption %}
こちらもあっさりと反映された
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}
