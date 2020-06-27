---
layout : post
title  : "Dockerに慣れる : ボリュームをマウントする"
date   : 2020/06/26
lastchange : 2020-06-28 00:49:35
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
---

## ホスト上のディレクトリを共有する

[Dockerに慣れる : Dockerfile について]({{site.github.url}}{% link _docs/Docker/basic-dockerfile.md %})
では

1. 元となるコンテナイメージに
2. 環境変数を設定したり
3. 特定のディレクトリに必要なファイルをコピーしたり
4. ファイルを編集したり

することでカスタムしたコンテナイメージを生成した。
だが、変更したい箇所を確認する度にコンテナイメージを生成するのは記憶領域が逼迫するし、
かったるい。

その辺りを解決する方法として、ホスト内の特定のファイルやディレクトリをコンテナプロセスと共有したりできるようだ。

今回はそれを試してみる。

参考:

* [【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
* [【Docker】Dockerでホストのディレクトリをマウントする - Qiita](https://qiita.com/Yarimizu14/items/52f4859027165a805630 "【Docker】Dockerでホストのディレクトリをマウントする - Qiita")


## バインドマウント

{% capture text %}

１．バインドマウント（bind）
: Dockerホストのファイルやディレクトリをコンテナ上にマウントする機能です。
  バインドマウントを行うと、コンテナの外にあるファイルを、コンテナの中から読み書き可能になります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ホストのディレクトリやファイルをコンテナプロセスにマウントするのをバインドマウントと言うらしい。
これが実現できると開発用仮想環境コンテナを作り、開発領域だけマウントして開発することができる。

これをやってみる。


{% capture text %}

## httpdコンテナでバインドマウントする

httpdコンテナに対し、Dockerホスト側に用意したindex.htmlをマウントさせてみます。
ファイルの配置と内容は以下です。

```
/home/[ユーザーディレクトリ]/docker_httpd/htdocs/index.html
```
 
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<meta http-equiv="content-type" charset="utf-8">
<html>
 <head>
  <title>Dockerマウントテスト</title>
 </head>
 <body>
  httpd container working!<br/>
  Dockerホストのhtmlファイルをマウントしています！<br/>
 </body>
</html>
```

では、コンテナを起動します。runコマンドに「-ｖ」オプションを指定します。

```sh
docker run -d -p 8080:80 -v /home/[ユーザーディレクトリ]/docker_httpd/htdocs/:/usr/local/apache2/htdocs/ httpd
```

-ｖオプションで、ホスト側の htdocs をコンテナ側の htdocs にマウントします。

```
-v <host_path>:<container_path>
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

適当なディレクトリを作り、内部に _index.html_ を作成する。
そのディレクトリをマウントするのを試してみる。

```sh
$ mkdir test
$ cd test/
$ mkdir htdocs
```

ディレクトリを作った。内部に _index.html_ を作成する。

```sh
$ cd htdocs
$ vimr index.html
```

```html
<!DOCTYPE html>
<html lang="ja-jp">
<head>
<meta charset="utf-8">
<title>
  Test page
</title>
</head>
<body>
  <p>
  This sentence wrote in host.
  </p>
</body>
</html>
```

マウントして実行してみる。

```sh
$ docker run -d -p 8080:80 -v htdocs/:/usr/local/apache2/htdocs/ httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
8559a31e96f4: Pull complete 
bd517d441028: Pull complete 
f67007e59c3c: Pull complete 
83c578481926: Pull complete 
f3cbcb88690d: Pull complete 
Digest: sha256:387f896f9b6867c7fa543f7d1a686b0ebe777ed13f6f11efc8b94bec743a1e51
Status: Downloaded newer image for httpd:latest
docker: Error response from daemon: create htdocs/: "htdocs/" includes invalid characters for a local volume name, only "[a-zA-Z0-9][a-zA-Z0-9_.-]" are allowed. If you intended to pass a host directory, use absolute path.
See 'docker run --help'.
```

最後にエラーが出て無理だった。
読んでみると、

* ファイルは相対パスで大丈夫
  * ただし、 `/` を使用できない、つまりコマンド実行と同階層にファイルがある必要がある
* ディレクトリは絶対パス指定をしなければならない

 ということだそうだ。

{% capture text %}

## ホスト側のマウント先を指定する場合

```sh
docker run -v [ホストディレクトリの絶対パス]:[コンテナの絶対パス] [イメージ名] [コマンド]
```

```sh
docker run -v /lib/modules:/lib/modules dummy-img /bin/bash
```

これでホスト側のディレクトリがコンテナにマウントされる。コンテナ上で作ったファイルがホストの方に残る。

`docker inspect` するとこんな感じで表示される↓

```json
"Mounts": [
    {
        "Source": "/lib/modules",
        "Destination": "/lib/modules",
        "Mode": "",
        "RW": true,
        "Propagation": "rprivate"
    }
]
```

マウントするとデフォルトで `read-write mode` なので `"RW": true` になってる。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】Dockerでホストのディレクトリをマウントする - Qiita](https://qiita.com/Yarimizu14/items/52f4859027165a805630 "【Docker】Dockerでホストのディレクトリをマウントする - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

こちらも絶対パスって書いてある。仕方ないので絶対パスにする。

```sh
 $ docker run -d -p 8080:80 -v /Users/test_user/sandbox/test/htdocs:/usr/local/apache2/htdocs httpd
69bbdaa723cb7eed234fd27c55f0a4e164e5a3a7f14ec013c953add4667148c9
```

ブラウザで確認してみる。 <http://localhost:8080>


{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-bind-mount/01_bind_first.png %}{% endcapture %}
{% capture caption %}
ホスト側で用意したコンテンツが表示された。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



{% capture text %}
ここで一旦、コンテナの中に入ってみます。

```sh
docker exec -it [コンテナID] /bin/bash
```

___

では、そのままコンテナ側からindex.htmlを編集してみます。

___

準備ができたら、index.htmlを編集します。以下（ピンクの行）の様に追記しました。

```sh
vi /usr/local/apache2/htdocs/index.html
```
 
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<meta http-equiv="content-type" charset="utf-8">
<html>
 <head>
  <title>Dockerマウントテスト</title>
 </head>
 <body>
  httpd container working!<br/>
  Dockerホストのhtmlファイルをマウントしています！<br/>
  マウント後にコンテナ側から更新した内容も、反映される<br/>
 </body>
</html>
```

内容を保存してブラウザをリロードすると、追記した内容が画面に反映されています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ](https://www.public.ne.jp/2019/02/08/docker-5/ "【Docker】第5回　マウントについて（bind）/札幌のAI・IoT・システム開発｜ITイノベーション/最先端技術｜パブリックリレーションズ")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ピンクの行の部分をうまく引用できてないが、内部から編集を行ってみる。
`vim` のインストールなどのログは省略。

```sh
$ docker exec -it funny_burnell /bin/bash
# apt update && apt upgrade && apt install vim
# vim htdocs/index.html 
```

```html
<!DOCTYPE html>
<html lang="ja-jp">
<head>
<meta charset="utf-8">
<title>
  Test page
</title>
</head>
<body>
  <p>
  This sentence wrote in host.
  </p>
  <p>
  This sentence wrote in container.
  </p>
</body>
</html>
```

```html
  <p>
  This sentence wrote in container.
  </p>
```

の部分を追加した。ブラウザで見てみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/basic-bind-mount/02_change_in_container.png %}{% endcapture %}
{% capture caption %}
コンテナ側の変更が反映されている
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

コンテナを `ctrl-p` `ctrl-q` で抜けてホスト側のファイルを確認してみる

```sh
# read escape sequence
$ cat htdocs/index.html 
```

```html
<!DOCTYPE html>
<html lang="ja-jp">
<head>
<meta charset="utf-8">
<title>
  Test page
</title>
</head>
<body>
  <p>
  This sentence wrote in host.
  </p>
  <p>
  This sentence wrote in container.
  </p>
</body>
</html>
```

変更が反映されている。

コンテナプロセスを終了した後 _index.html_ がどうなっているか確認する。

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
69bbdaa723cb        httpd               "httpd-foreground"   19 minutes ago      Up 19 minutes       0.0.0.0:8080->80/tcp   funny_burnell
$ docker rm -f funny_burnell
funny_burnell
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ cat htdocs/index.html 
```

```html
<!DOCTYPE html>
<html lang="ja-jp">
<head>
<meta charset="utf-8">
<title>
  Test page
</title>
</head>
<body>
  <p>
  This sentence wrote in host.
  </p>
  <p>
  This sentence wrote in container.
  </p>
</body>
</html>
```

コンテナ内で変更したものも問題なくそのまま保存されている。