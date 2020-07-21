---
layout : post
title  : "ASP.NET Core SDK コンテナにディレクトリをマウントする"
date   : 2020/07/17
lastchange : 2020-07-21 15:55:04
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

参考:
* [[bash] 実行スクリプトの絶対パスの取得 - Qiita](https://qiita.com/koara-local/items/2d67c0964188bba39e29 "[bash] 実行スクリプトの絶対パスの取得 - Qiita")


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


{% capture text %}

### docker buildしたときに、Dockerfileがシンボリックリンクだと失敗する。
タイトルのまんまなんだけど、docker buildしたときに、Dockerfileがシンボリックリンクだと失敗する。こんなエラーね。

```sh
% docker build -t image-name:tag-name .

unable to prepare context: The Dockerfile () must be within the build context (.)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[みんな忘れてしまうんだ: docker buildしたときに、Dockerfileがシンボリックリンクだと失敗する。](https://choni-waniwani.blogspot.com/2016/10/docker-builddockerfile_6.html "みんな忘れてしまうんだ: docker buildしたときに、Dockerfileがシンボリックリンクだと失敗する。")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


この通り Docker はシンボリックリンクを読まなかった。

また、 dotnet の sdk コンテナを含めた設定ファイルから `docker-compose up` をしても 
SDK コンテナは起動直後に終了してしまうことが確認できた。
これはこのページ下の方にまとめを書いた。

そんなこんなでシェルスクリプトを作っていく。
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

## docker-compose から dotnet SDK コンテナを起動しても即座に終了してしまう

ディレクトリに
[dotnet-docker/Dockerfile](https://github.com/dotnet/dotnet-docker/blob/master/src/aspnet/3.1/alpine3.12/amd64/Dockerfile "dotnet-docker/Dockerfile")
をコピーしてきて、 _docker-compose.yml_ を以下のように記述した。

```yml
version : "3"

services :
  dotnet_sdk : 
    build : .
    ports :
      - 5000:5000
    volumes:
      - ./Buff:/home/Project
```

これで `docker-compose up` をしても即座に終了してしまう。

```sh
$ docker-compose up
Creating network "test_default" with the default driver
Building dotnet_sdk
Step 1/6 : ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
Step 2/6 : FROM $REPO:3.1-alpine3.12
 ---> e4ede6c1c5d4
Step 3/6 : ENV     ASPNETCORE_URLS=     DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false     DOTNET_USE_POLLING_FILE_WATCHER=true     LC_ALL=en_US.UTF-8     LANG=en_US.UTF-8     NUGET_XMLDOC_MODE=skip     POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
 ---> Using cache
 ---> e4f9db3acb3f
Step 4/6 : RUN apk add --no-cache icu-libs
 ---> Using cache
 ---> 6ab5cc90721f
Step 5/6 : RUN dotnet_sdk_version=3.1.302     && wget -O dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Sdk/$dotnet_sdk_version/dotnet-sdk-$dotnet_sdk_version-linux-musl-x64.tar.gz     && dotnet_sha512='ba6731051604b141c9a18e8d52eca383089aa524c07eb74e84acaa55334703a6636a054bea7c644b28f39a57fd19547ad92459415e4d25a85bb6ab3ff4046a19'     && echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c -     && mkdir -p /usr/share/dotnet     && tar -C /usr/share/dotnet -oxzf dotnet.tar.gz ./packs ./sdk ./templates ./LICENSE.txt ./ThirdPartyNotices.txt     && rm dotnet.tar.gz     && dotnet help
 ---> Using cache
 ---> 36cf3c4ae90e
Step 6/6 : RUN powershell_version=7.0.1     && wget -O PowerShell.Linux.Alpine.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.Alpine.$powershell_version.nupkg     && powershell_sha512='a2390780d098e686c9e43a3ade215240ac7699904807cebcdb00804248b88f1afb8592aca29d28d3a9c9294534016a6d5c4014e7d0f2c2865518c32be02b8368'     && echo "$powershell_sha512  PowerShell.Linux.Alpine.$powershell_version.nupkg" | sha512sum -c -     && mkdir -p /usr/share/powershell     && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.Alpine     && dotnet nuget locals all --clear     && rm PowerShell.Linux.Alpine.$powershell_version.nupkg     && chmod 755 /usr/share/powershell/pwsh     && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh     && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm     && apk add --no-cache ncurses-terminfo-base
 ---> Using cache
 ---> 0b356b6b46ba
Successfully built 0b356b6b46ba
Successfully tagged test_dotnet_sdk:latest
WARNING: Image for service dotnet_sdk was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating test_dotnet_sdk_1 ... done
Attaching to test_dotnet_sdk_1
test_dotnet_sdk_1 exited with code 0
```

即座に終了してしまう。 `docker-compose ps` を確認してみる。

```sh
$ docker-compose ps
      Name          Command   State    Ports
--------------------------------------------
test_dotnet_sdk_1   /bin/sh   Exit 0        
```

`docker ps -a` してみる

```sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
42dc25a3f813        test_dotnet_sdk     "/bin/sh"           48 seconds ago      Exited (0) 47 seconds ago                       test_dotnet_sdk_1
```

落ちてる・・・。
