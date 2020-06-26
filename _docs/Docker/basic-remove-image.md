---
layout : post
title  : "Dockerに慣れる : 不要なコンテナイメージを消去する"
date   : 2020/06/26
lastchange : 2020-06-26 11:12:46
tags   :
  - docker
  - httpd
  - container
  - docker rmi
---

## コンテナイメージが溜まっていってしまう

[Dockerに慣れる : 変更したコンテナイメージを保存する]({{site.github.url}}{% link _docs/Docker/basic-commit-container.md %})
でコンテナイメージを作成することによって変更を保存したが、そうするとコンテナイメージが増えていく。

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.2               e51586e6a3bf        11 hours ago        221MB
httpd_test          0.0.1               9ae8cc7c6375        12 hours ago        221MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

増えてしまって不要になったコンテナイメージを消去する方法を調べた。

参考:

* [Dockerイメージとコンテナの削除方法 - Qiita](https://qiita.com/tifa2chan/items/e9aa408244687a63a0ae "Dockerイメージとコンテナの削除方法 - Qiita")
* [rmi — Docker-docs-ja 17.06 ドキュメント](http://docs.docker.jp/engine/reference/commandline/rmi.html "rmi — Docker-docs-ja 17.06 ドキュメント")





## コンテナイメージを消去する




{% capture text %}

### イメージの削除

* コマンド： `docker rmi [イメージID]`
* 実際にdocker-whale削除しようと実行してみるとコンテナが存在しているというメッセージが表示される

```sh
$ docker rmi 2f37bab81128
Error response from daemon: Conflict, cannot delete 2f37bab81128 because the container b07c7c83b892 is using it, use -f to force
Error: failed to remove images: [2f37bab81128]
```

* forceオプションをつけてみると消えた（コンテナが先に消えていればforceオプションは不要）
  ちなみに、なんでこんなにいっぱいのファイルが削除されるんだろう？と思ったら中間ファイルらしい。
  `docker images -a` で中間ファイルが確認できる。

```sh
$ docker rmi -f 2f37bab81128 
Untagged: docker-whale:latest
Deleted: 2f37bab81128991f9e024bc1064be806cd3bf591e8d269d9ceea8f4f768b414e
Deleted: 7191e8874482e349e2fb04ccb4c15b925439a8c884e3a266bd41adb324dd9f9a
Deleted: e36145689a651426e87b3d906273c2c06aacd470d0953061f8d9bc00015d9d5c
Deleted: f71b492d24edf7b8e065e2debc0f2eb6502524f8d5f8eb6bb89b2eacd187c2cf
```

* 削除されたか確認

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
hello-world         latest              0a6ba66e537a        2 weeks ago         960 B
docker/whalesay     latest              ded5e192a685        5 months ago        247 MB
```

* forceでイメージを削除した場合でも、コンテナのほうは残っているみたい

```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
b07c7c83b892        docker-whale        "/bin/sh -c '/usr/gam"   10 minutes ago      Exited (0) 10 minutes ago                       clever_ptolemy
```

コンテナを削除してからイメージを削除するのがきれいですね。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Dockerイメージとコンテナの削除方法 - Qiita](https://qiita.com/tifa2chan/items/e9aa408244687a63a0ae "Dockerイメージとコンテナの削除方法 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ざっくり

* `docker rmi` コマンドで消せる
  * 無理矢理消す場合は `docker rmi -f`
* コンテナプロセスが動作していると消せない


{% capture text %}

ショート ID かロング ID、タグ、digest を使ってイメージを削除できます。
イメージがタグによって参照されている場合、イメージを削除する前にそれらの削除が必要です。
Digest の参照値はイメージのタグを削除する時、自動的に削除されます。

```sh
$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
test1                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test2                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)

$ docker rmi fd484f19954f
Error: Conflict, cannot delete image fd484f19954f because it is tagged in multiple repositories, use -f to force
2013/12/11 05:47:16 Error: failed to remove one or more images

$ docker rmi test1
Untagged: test1:latest
$ docker rmi test2
Untagged: test2:latest

$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
$ docker rmi test
Untagged: test:latest
Deleted: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[rmi — Docker-docs-ja 17.06 ドキュメント](http://docs.docker.jp/engine/reference/commandline/rmi.html "rmi — Docker-docs-ja 17.06 ドキュメント")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ざっくり

* 同じコンテナイメージ ID で複数のタグ(バージョンなど)が登録されているマルチレポジトリの場合、まずはタグの方を消す必要がある

とりあえず、 `nginx` を消してみる。動作している `nginx` コンテナプロセスがないかチェックする


```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

コンテナイメージ一覧でコンテナイメージ ID を確認する

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.2               e51586e6a3bf        11 hours ago        221MB
httpd_test          0.0.1               9ae8cc7c6375        12 hours ago        221MB
nginx               latest              2622e6cca7eb        2 weeks ago         132MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

`nginx` のコンテナイメージ ID は `2622e6cca7eb` だった。これを削除してみる


```sh
$ docker rmi 2622e6cca7eb
Untagged: nginx:latest
Untagged: nginx@sha256:21f32f6c08406306d822a0e6e8b7dc81f53f336570e852e25fbe1e3e3d0d0133
Deleted: sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163
Deleted: sha256:e86d1b8b130bec203609b4b1d7b851bd763fa16e513e5a3fa6102ebea23260e0
Deleted: sha256:8f9f007533543813bb1aef80b791a16e5e16c7cbbbc456a3a483d0fa7a9effcc
Deleted: sha256:e2c0065a77fee75795cdcf9f19a72f11769332423cd52ec9e19aacfb878aec8b
Deleted: sha256:059442698ef65fe8545e4fe9657988a10329b9c3663b368ae7ee0007a9c43949
```

そこそこ中間イメージがあったらしい。実際に消えたか確認してみる。

```sh
$ docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.2               e51586e6a3bf        23 hours ago        221MB
httpd_test          0.0.1               9ae8cc7c6375        23 hours ago        221MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

`httpd_test` も消去してみる

```sh
$ docker rmi httpd_test:0.0.1
Untagged: httpd_test:0.0.1
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd_test          0.0.2               e51586e6a3bf        23 hours ago        221MB
httpd               latest              ccbcea8a6757        2 weeks ago         166MB

```

`httpd_test:0.0.1` を消しただけではタグが消えただけのようだ。 `httpd_test:0.0.2` も消去してみる。

```sh
$ docker rmi httpd_test:0.0.2
Untagged: httpd_test:0.0.2
Deleted: sha256:e51586e6a3bffe7a405af9311a1b319807770e5e0fbccf6c08776ddbcc012064
Deleted: sha256:482d158bcd884a66f9a43fb93dda4aa48806a39319638b3b0d79e666d0e97ca4
Deleted: sha256:9ae8cc7c63757925229884abe20d2214be71cb2a6be468aec41fc8fb52760580
Deleted: sha256:aa8e6da6ff3f73f9b68465a37f041a6f89bbd22ef4d522821d0e3437092f0199
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              ccbcea8a6757        2 weeks ago         166MB
```

全てのタグが消えることでコンテナイメージも消えるようだ。

### 注意点

コンテナイメージとして変更を保存していると、コンテナを消去する＝変更内容を消去することになる。
消去するコンテナイメージをちゃんと確認する。
