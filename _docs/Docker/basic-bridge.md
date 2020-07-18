---
layout : post
title  : "Dockerに慣れる : コンテナ間のネットワークについて"
date   : 2020/07/19
lastchange : 2020-07-19 02:20:21
tags   :
  - docker
  - compose
  - network
  - container
  - bridge
---

## コンテナ間の通信はどうするのか

コンテナで閉じているのだから、普通は通信できないはず。
だけれども普通にサービス間通信ができるらしい。どういう仕組みなのか、少し調べた。

参考:

* [記録:Dockerコンテナ間通信(bridge) - Qiita](https://qiita.com/mincoshi/items/5a1542b523195a9c11ed "記録:Dockerコンテナ間通信(bridge) - Qiita")
* [Docker勉強メモ④ コンテナ間通信 - Qiita](https://qiita.com/zgw426/items/bb42f6bd17707de354b9 "Docker勉強メモ④ コンテナ間通信 - Qiita")
 

## インストール時に作られるネットワーク

{% capture text %}

#### 0.デフォルトネットワークの確認(はじめに)

DockerはDocker独自のネットワークを生成する。
デフォルトで生成されるネットワークが `bridge` ドライバを使った ***bridge*** ネットワーク。

で、新規ネットワークでの通信を試す前にこの bridge ネットワークをまずは確認してみる。

ネットワーク廻りには `network` コマンドを利用する。ネットワークの一覧を表示するのは`network ls` で確認できる。
ホストでの操作

host$ sudo docker network ls
NETWORK ID          NAME                                        DRIVER              SCOPE
1df7cecfc3a4        bridge                                      bridge              local
65f55a9eacca        host                                        host                local
2f1081153878        none                                        null                local

なにも触っていなければ上記の3つが設定される。
デフォルトネットワークの ***bridge*** とホストが所属するネットワークの ***host*** と
ネットワーク所属なしを意味する ***none*** だ。
特にオプションとして指定が無ければ `docker run` で生成されるコンテナは ***bridge*** に所属する。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[記録:Dockerコンテナ間通信(bridge) - Qiita](https://qiita.com/mincoshi/items/5a1542b523195a9c11ed "記録:Dockerコンテナ間通信(bridge) - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

勝手に作られるネットワークを確認してみる。

```sh
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
8ad39c40354c        bridge              bridge              local
a625db98bfba        host                host                local
a132c6a6d16b        none                null                local
```

同様の表示がされた。

## コンテナ間の通信方法

コンテナ間の通信はコンテナ名で行うらしい。
コンテナによっては通信先のコンテナ名がデフォルトで入っており、
それに合わせることで簡単に動作できるようになっているようだ。


{% capture text %}

* DBとWebサーバのコンテナを作る
* 同じネットワーク wordpress-network を指定する
* MySQLのコンテナ名は「mysql」とする
  * WordPressのDockerイメージは、MySQLへの接続先の指定のホスト名が「mysql」だから
  * これで、WordPressコンテナ（wordpress）からMySQLコンテナ（mysql）に向けて通信可になる


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Docker勉強メモ④ コンテナ間通信 - Qiita](https://qiita.com/zgw426/items/bb42f6bd17707de354b9 "Docker勉強メモ④ コンテナ間通信 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

#### 3.通信する

netcatで通信してみる。

サーバー側は待ち受ける

```sh
test-server$ nc -lp 8080
```

クライアント側から送る
```sh
test-client$ nc 172.19.0.2 8080
hello
```

サーバー側で確認

```sh
test-server$ nc -lp 8080
hello
```

通信できた。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[記録:Dockerコンテナ間通信(bridge) - Qiita](https://qiita.com/mincoshi/items/5a1542b523195a9c11ed "記録:Dockerコンテナ間通信(bridge) - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}
