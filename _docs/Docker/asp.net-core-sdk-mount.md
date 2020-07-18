---
layout : post
title  : "ASP.NET Core SDK コンテナにディレクトリをマウントする"
date   : 2020/07/17
lastchange : 2020-07-19 01:49:20
tags   :
  - docker
  - compose
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## ASP.NET Core コンテナにディレクトリをマウントし、プロジェクトの変更を残す

[ASP.NET Core SDK コンテナでWebアプリを動かす]({{site.github.url}}{% link _docs/Docker/asp.net-core-sdk-webapp.md %})
では WebApp のプロジェクトを作り、コンテナ外からアクセスして動作確認までを行った。

今回はホストのディレクトリをマウントし、そこにプロジェクトを保存するように変更し、実際にプロジェクトを変更してみる。

## プロジェクトディレクトリを作成する

<https://github.com/leico/dotnet-study> というリポジトリを作った。

これをローカルに持ってきて移動する

```sh
$ git clone https://github.com/leico/dotnet-study.git
Cloning into 'dotnet-study'...
warning: You appear to have cloned an empty repository.
$ cd dotnet-study
```

## dotnet-docker を submodule として登録する

公式の変更内容に手軽に追従したいので dotnet-docker を submodule で登録する。
登録できたらひとまず push しておく。一時保存重要。

```sh
$ git submodule add https://github.com/dotnet/dotnet-docker.git
Cloning into '/Users/leico_studio/Project/public_git/dotnet-study/dotnet-docker'...
remote: Enumerating objects: 524, done.
remote: Counting objects: 100% (524/524), done.
remote: Compressing objects: 100% (198/198), done.
remote: Total 22597 (delta 197), reused 426 (delta 127), pack-reused 22073
Receiving objects: 100% (22597/22597), 4.79 MiB | 3.79 MiB/s, done.
Resolving deltas: 100% (10164/10164), done.

$ ls
dotnet-docker

$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   .gitmodules
	new file:   dotnet-docker

$ git commit
[master (root-commit) a5d3c84] add dotnet-docker as submodule
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 dotnet-docker

$ git push
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 341 bytes | 341.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/leico/dotnet-study.git
 * [new branch]      master -> master
```

## 作業用ディレクトリを作成する

作業用ディレクトリを作る。今回は _WebApp_ とした

```sh
$ mkdir WebApp
```

## SDK 用コンテナを build するスクリプトを作る

docker-compose やら Dockerfile にシンボリックリンクを貼るやら、色々試したがうまくいかず、
最終的に image をビルドするためのスクリプト、イメージを動作させるためのスクリプトに落ち着いた。

リポジトリルートに SDK コンテナを build するスクリプトを作成する。

```sh
$ vimr create_SDK_image.sh
```

```sh
#!/bin/sh

docker build --pull -t dotnetsdk3.1 ./dotnet-docker/src/sdk/3.1/alpine3.12/amd64
```

実行権限を与えて実行する

```sh
$ chmod +x create_SDK_image.sh 
$ ./create_SDK_image.sh 
Sending build context to Docker daemon  4.608kB
Step 1/6 : ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
Step 2/6 : FROM $REPO:3.1-alpine3.12
~~~(中略)~~~
Successfully tagged dotnetsdk3.1:latest
```

完了した。

## 作業用ディレクトリをマウントしながら、 SDK 用コンテナを実行するスクリプトを作る

こちらもリポジトリルートに image を実行するためのスクリプトを作成する。

```sh
$ vimr run_SDK_container.sh
```

```sh
#!/bin/sh

#get absolute path of directory contained this script
SCRIPT_DIR=$(cd $(dirname $0); pwd)


docker run --rm -it -p 5000:5000 -v $SCRIPT_DIR/WebApp:/home/WebApp dotnetsdk3.1
```

`SCRIPT_DIR` にはスクリプトが存在するディレクトリの絶対パスが入る。このパスを利用してボリュームをマウントする。

スクリプトを格納しているディレクトリのパスを取得するコードはこちらを利用した

{% capture text %}

# 実装

```sh
SCRIPT_DIR=$(cd $(dirname $0); pwd)
```

* `$0` で実行コマンドを取得
* `dirname` でベースディレクトリを取得
* サブシェル内で `cd` で移動
* `pwd` で絶対パスの取得

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[[bash] 実行スクリプトの絶対パスの取得 - Qiita](https://qiita.com/koara-local/items/2d67c0964188bba39e29 "[bash] 実行スクリプトの絶対パスの取得 - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

実行権限を付与して実行。 SDK コンテナに入る

```sh
$ chmod +x run_SDK_container.sh 
$ ./run_SDK_container.sh 
/ # ls
```

無事に入れた。

## マウントしているディレクトリにプロジェクトを作成する

マウントしたディレクトリで前回同様のコマンドをやってみる。
まずはディレクトリに移動する

```sh
# cd home/WebApp/
```

プロジェクトをテンプレートから作成する

```sh
# dotnet new webApp
Getting ready...
The template "ASP.NET Core Web App" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/aspnetcore/3.1-third-party-notices for details.

Processing post-creation actions...
Running 'dotnet restore' on /home/WebApp/WebApp.csproj...
  Determining projects to restore...
  Restored /home/WebApp/WebApp.csproj (in 140 ms).

Restore succeeded.
```

成功した。ホスト側から確認してみる。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/asp.net-core-sdk-mount/01_extract.png %}{% endcapture %}
{% capture caption %}
無事にホスト側から確認できた
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## ホスト側から変更を加え、コンテナで実行する

前回と同様の変更を加え、コンテナで実行し、ホスト側からブラウザでアクセスして確認する。

まずはディレクトリに移動し、ソースを変更する

```sh
$ cd WebApp 
$ vimr Program.cs 
```

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

namespace WebApp
{
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>()
                    .UseUrls("http://0.0.0.0:5000"); // add this code
                });
    }
}
```

コンテナ側からプロジェクトを実行する

```sh
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
warn: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[35]
      No XML encryptor configured. Key {ddfb0620-754d-4541-9a5a-4f8607a3e027} may be persisted to storage in unencrypted form.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://0.0.0.0:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /home/WebApp
```

動作したらしい。ホスト側から <http://localhost:5000> にアクセスする

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/asp.net-core-sdk-mount/02_webapp.png %}{% endcapture %}
{% capture caption %}
無事アクセスできた
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}
