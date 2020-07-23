---
layout : post
title  : "Docker 内にマウントするディレクトリ構成を変更する"
date   : 2020-07-23
lastchange : 2020-07-23 15:07:26
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## Micorsoft のチュートリアルを進めるために、ディレクトリ構成を変更する

[ASP.NET Core SDK コンテナで動作しているプログラムを変更する]({{site.github.url}}{% link _docs/Docker/asp.net-core-sdk-hw.md %})
までで、最初のチュートリアルが終了した。引き続き Microsoft 公式のチュートリアルを Docker で進めていこうと思うのだが、 _WebApp_ ディレクトリとは別のディレクトリで進めて行きたい。

一方で、 _WebApp_ のプロジェクトも引き続き Docker にマウントしておきたいので、ディレクトリ構成を変更することにした。

やることは

1. ディレクトリ構成の変更
2. 起動用スクリプトの修正

となる。


## ホスト側のディレクトリ構成を変更する

まずはホスト側のディレクトリ構成を変更する。 一つ上位のディレクトリを作成し、
_WebApp_ ディレクトリをその中に移動させる。公式のチュートリアル用のディレクトリも後ほど
上位ディレクトリの中に作成する。

上位ディレクトリの名前は今回 _Projects_ とした。

```sh
$ mkdir Projects  
$ ls
Projects		WebApp			create_SDK_image.sh	dotnet-docker		run_SDK_container.sh
$ mv WebApp Projects 
```

## 起動用スクリプトを編集する

起動用スクリプトを以下のように変更した。

```sh
$ vimr run_SDK_container.sh 
```

```sh
#!/bin/sh

#get absolute path of directory contained this script
SCRIPT_DIR=$(cd $(dirname $0); pwd)


docker run --rm -it -p 5000:5000 -v $SCRIPT_DIR/Projects:/Projects dotnetsdk3.1
```

_Projects_ ディレクトリを コンテナ内ルート直下にマウントするようにした。

起動してみる。

```sh
$ ./run_SDK_container.sh
#   
```

起動した。ディレクトリを確認してみる。

```sh
/ # ls 
Projects  dev       home      media     opt       root      sbin      sys       usr
bin       etc       lib       mnt       proc      run       srv       tmp       var
```

_Projects_ ディレクトリがある。中身を確認する。

```sh
/ # ls Projects/
WebApp
/ # 
```

大丈夫そうだ。

