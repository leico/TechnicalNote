---
layout : post
title  : "利用したモジュールをインストールした状態のコンテナイメージを作成する"
date   : 2020-07-30
lastchange : 2020-07-30 20:30:42
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## コンテナを終了すると変更が全て元に戻ってしまう

コンテナ内で、マウントしていない領域に反映されるコマンドは、コンテナが終了すると元に戻ってしまう。
それは前回、 [Model を追加する]({{site.github.url}}{% link _docs/AspDotNetCore/add-model.md %}) 
の変更も同じだ。

今回は、前回内部で実行したコマンドを反映させたコンテナイメージを作成する。

参考 : 

* [DockerfileでPATHを通す時はRUNではなくENVを使おう - Qiita](https://qiita.com/TakashiOshikawa/items/2b8974c242a7054e0be5 "DockerfileでPATHを通す時はRUNではなくENVを使おう - Qiita")

## 公式のコンテナイメージをコピーして変更する

今まで公式が配布しているコンテナイメージをそのまま使ってコンテナをビルドしてきたが、
今回は変更を加えてビルドすることにする。
なのでまずはディレクトリを作成してそこにコピーする。最終的に実行したコマンドを全て統合し、
1つのシェルスクリプトにする。

まずはコピーする先のディレクトリを作成する。

```sh
$ mkdir Dockerfiles
```

ここに公式の _Dockerfile_ をコピーしてくる

```sh
$ cp dotnet-docker/src/sdk/3.1/alpine3.12/amd64/Dockerfile Dockerfiles/Dockerfile_RizorPage
```

コピーできたらここに必要なプラグイン情報を追記してゆく
さらに、環境変数に追加するものがあるので、それも設定する

```sh
cat << 'EOF__' >> Dockerfiles/Dockerfile_RizorPage

RUN dotnet tool install --global dotnet-ef \
 && dotnet tool install --global dotnet-aspnet-codegenerator

ENV PATH $PATH:/root/.dotnet/tools

EOF__
```

`ENV` でパスを通すのはここが参考になった。

{% capture text %}

## buildに成功したDockerfile

そこで調べてみると
<http://stackoverflow.com/questions/27093612/in-a-dockerfile-how-to-update-path-environment-variable>
<http://inokara.hateblo.jp/entry/2013/12/28/121828>

DockerfileのPATHはRUNではなくENVで通すことを知りDockerfileを書き換えました。

```dockerfile
FROM takashioshikawa/java8

WORKDIR /tmp

RUN wget http://downloads.typesafe.com/scala/2.11.7/scala-2.11.7.tgz
RUN tar xvzf scala-2.11.7.tgz
RUN mkdir /usr/local/scala
RUN mv scala-2.11.7 /usr/local/scala
ENV PATH $PATH:/usr/local/scala/scala-2.11.7/bin
RUN echo $PATH

RUN which scala

CMD /bin/bash
```

`RUN export PATH=$PATH:/usr/local/scala/scala-2.11.7/bin`

を

`ENV PATH $PATH:/usr/local/scala/scala-2.11.7/bin`

に変更しただけです。
これでビルドが通るようになり

`docker --rm -it --name testscala takashioshikawa/scala-2.11.7`
でコンテナを立ち上げてwhichしてみるとちゃんとScalaが使えるようになりました。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[DockerfileでPATHを通す時はRUNではなくENVを使おう - Qiita](https://qiita.com/TakashiOshikawa/items/2b8974c242a7054e0be5 "DockerfileでPATHを通す時はRUNではなくENVを使おう - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ビルドしてみる。

```sh
$ docker build --pull -f ./Dockerfiles/Dockerfile_RizorPage -t test ./Dockerfiles
Sending build context to Docker daemon  4.608kB
Step 1/8 : ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
Step 2/8 : FROM $REPO:3.1-alpine3.12
3.1-alpine3.12: Pulling from dotnet/core/aspnet
df20fa9351a1: Pull complete 
ac47820464eb: Pull complete 
c991f9f4493d: Pull complete 
bb9fc6048a9d: Pull complete 
Digest: sha256:a1a68872445e04f3ff3128b15a6dbf3a935630607d91f5830433c14a7b51560a
Status: Downloaded newer image for mcr.microsoft.com/dotnet/core/aspnet:3.1-alpine3.12
 ---> e4ede6c1c5d4
Step 3/8 : ENV     ASPNETCORE_URLS=     DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false     DOTNET_USE_POLLING_FILE_WATCHER=true     LC_ALL=en_US.UTF-8     LANG=en_US.UTF-8     NUGET_XMLDOC_MODE=skip     POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
 ---> Running in 6c5786c24828
Removing intermediate container 6c5786c24828
 ---> 13b50ac2ddc0
Step 4/8 : RUN apk add --no-cache icu-libs
 ---> Running in 6daf15689a3f
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/1) Installing icu-libs (67.1-r0)
OK: 42 MiB in 24 packages
Removing intermediate container 6daf15689a3f
 ---> 09ea4f616b72
Step 5/8 : RUN dotnet_sdk_version=3.1.302     && wget -O dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Sdk/$dotnet_sdk_version/dotnet-sdk-$dotnet_sdk_version-linux-musl-x64.tar.gz     && dotnet_sha512='ba6731051604b141c9a18e8d52eca383089aa524c07eb74e84acaa55334703a6636a054bea7c644b28f39a57fd19547ad92459415e4d25a85bb6ab3ff4046a19'     && echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c -     && mkdir -p /usr/share/dotnet     && tar -C /usr/share/dotnet -oxzf dotnet.tar.gz ./packs ./sdk ./templates ./LICENSE.txt ./ThirdPartyNotices.txt     && rm dotnet.tar.gz     && dotnet help
 ---> Running in 74862f7b6e2d
Connecting to dotnetcli.azureedge.net (117.18.232.200:443)
saving to 'dotnet.tar.gz'
dotnet.tar.gz          4% |*                               | 5711k  0:00:19 ETA
dotnet.tar.gz         27% |********                        | 31.2M  0:00:05 ETA
dotnet.tar.gz         46% |**************                  | 52.7M  0:00:03 ETA
dotnet.tar.gz         66% |*********************           | 76.2M  0:00:01 ETA
dotnet.tar.gz         87% |****************************    | 99.7M  0:00:00 ETA
dotnet.tar.gz        100% |********************************|  113M  0:00:00 ETA
'dotnet.tar.gz' saved
dotnet.tar.gz: OK

Welcome to .NET Core 3.1!
---------------------
SDK Version: 3.1.302

Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. The data is anonymous. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_CLI_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.

Read more about .NET Core CLI Tools telemetry: https://aka.ms/dotnet-cli-telemetry

----------------
Explore documentation: https://aka.ms/dotnet-docs
Report issues and find source on GitHub: https://github.com/dotnet/core
Find out what's new: https://aka.ms/dotnet-whats-new
Learn about the installed HTTPS developer cert: https://aka.ms/aspnet-core-https
Use 'dotnet --help' to see available commands or visit: https://aka.ms/dotnet-cli-docs
Write your first app: https://aka.ms/first-net-core-app
--------------------------------------------------------------------------------------
.NET Core SDK (3.1.302)
Usage: dotnet [runtime-options] [path-to-application] [arguments]

Execute a .NET Core application.

runtime-options:
  --additionalprobingpath <path>   Path containing probing policy and assemblies to probe for.
  --additional-deps <path>         Path to additional deps.json file.
  --fx-version <version>           Version of the installed Shared Framework to use to run the application.
  --roll-forward <setting>         Roll forward to framework version  (LatestPatch, Minor, LatestMinor, Major, LatestMajor, Disable).

path-to-application:
  The path to an application .dll file to execute.

Usage: dotnet [sdk-options] [command] [command-options] [arguments]

Execute a .NET Core SDK command.

sdk-options:
  -d|--diagnostics  Enable diagnostic output.
  -h|--help         Show command line help.
  --info            Display .NET Core information.
  --list-runtimes   Display the installed runtimes.
  --list-sdks       Display the installed SDKs.
  --version         Display .NET Core SDK version in use.

SDK commands:
  add               Add a package or reference to a .NET project.
  build             Build a .NET project.
  build-server      Interact with servers started by a build.
  clean             Clean build outputs of a .NET project.
  help              Show command line help.
  list              List project references of a .NET project.
  msbuild           Run Microsoft Build Engine (MSBuild) commands.
  new               Create a new .NET project or file.
  nuget             Provides additional NuGet commands.
  pack              Create a NuGet package.
  publish           Publish a .NET project for deployment.
  remove            Remove a package or reference from a .NET project.
  restore           Restore dependencies specified in a .NET project.
  run               Build and run a .NET project output.
  sln               Modify Visual Studio solution files.
  store             Store the specified assemblies in the runtime package store.
  test              Run unit tests using the test runner specified in a .NET project.
  tool              Install or manage tools that extend the .NET experience.
  vstest            Run Microsoft Test Engine (VSTest) commands.

Additional commands from bundled tools:
  dev-certs         Create and manage development certificates.
  fsi               Start F# Interactive / execute F# scripts.
  sql-cache         SQL Server cache command-line tools.
  user-secrets      Manage development user secrets.
  watch             Start a file watcher that runs a command when files change.

Run 'dotnet [command] --help' for more information on a command.
Removing intermediate container 74862f7b6e2d
 ---> 3d3c24b75498
Step 6/8 : RUN powershell_version=7.0.1     && wget -O PowerShell.Linux.Alpine.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.Alpine.$powershell_version.nupkg     && powershell_sha512='a2390780d098e686c9e43a3ade215240ac7699904807cebcdb00804248b88f1afb8592aca29d28d3a9c9294534016a6d5c4014e7d0f2c2865518c32be02b8368'     && echo "$powershell_sha512  PowerShell.Linux.Alpine.$powershell_version.nupkg" | sha512sum -c -     && mkdir -p /usr/share/powershell     && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.Alpine     && dotnet nuget locals all --clear     && rm PowerShell.Linux.Alpine.$powershell_version.nupkg     && chmod 755 /usr/share/powershell/pwsh     && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh     && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm     && apk add --no-cache ncurses-terminfo-base
 ---> Running in 356908ce5b04
Connecting to pwshtool.blob.core.windows.net (52.191.176.36:443)
saving to 'PowerShell.Linux.Alpine.7.0.1.nupkg'
PowerShell.Linux.Alp   0% |                                |  127k  0:01:45 ETA
PowerShell.Linux.Alp  17% |*****                           | 2335k  0:00:09 ETA
PowerShell.Linux.Alp  30% |*********                       | 4096k  0:00:06 ETA
PowerShell.Linux.Alp  42% |*************                   | 5696k  0:00:05 ETA
PowerShell.Linux.Alp  55% |*****************               | 7472k  0:00:04 ETA
PowerShell.Linux.Alp  67% |*********************           | 9152k  0:00:02 ETA
PowerShell.Linux.Alp  81% |**************************      | 10.7M  0:00:01 ETA
PowerShell.Linux.Alp  90% |*****************************   | 12.0M  0:00:00 ETA
PowerShell.Linux.Alp 100% |********************************| 13.2M  0:00:00 ETA
'PowerShell.Linux.Alpine.7.0.1.nupkg' saved
PowerShell.Linux.Alpine.7.0.1.nupkg: OK
You can invoke the tool using the following command: pwsh
Tool 'powershell.linux.alpine' (version '7.0.1') was successfully installed.
Clearing NuGet HTTP cache: /root/.local/share/NuGet/v3-cache
Clearing NuGet global packages folder: /root/.nuget/packages/
Clearing NuGet Temp cache: /tmp/NuGetScratch
Clearing NuGet plugins cache: /root/.local/share/NuGet/plugins-cache
Local resources cleared.
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/1) Installing ncurses-terminfo-base (6.2_p20200523-r0)
OK: 42 MiB in 25 packages
Removing intermediate container 356908ce5b04
 ---> 7f346dc13a09
Step 7/8 : RUN dotnet tool install --global dotnet-ef  && dotnet tool install --global dotnet-aspnet-codegenerator
 ---> Running in 966fb4ffb6c4
Tools directory '/root/.dotnet/tools' is not currently on the PATH environment variable.
If you are using bash, you can add it to your profile by running the following command:

cat << \EOF >> ~/.bash_profile
# Add .NET Core SDK tools
export PATH="$PATH:/root/.dotnet/tools"
EOF

You can add it to the current session by running the following command:

export PATH="$PATH:/root/.dotnet/tools"

You can invoke the tool using the following command: dotnet-ef
Tool 'dotnet-ef' (version '3.1.6') was successfully installed.
Tools directory '/root/.dotnet/tools' is not currently on the PATH environment variable.
If you are using bash, you can add it to your profile by running the following command:

cat << \EOF >> ~/.bash_profile
# Add .NET Core SDK tools
export PATH="$PATH:/root/.dotnet/tools"
EOF

You can add it to the current session by running the following command:

export PATH="$PATH:/root/.dotnet/tools"

You can invoke the tool using the following command: dotnet-aspnet-codegenerator
Tool 'dotnet-aspnet-codegenerator' (version '3.1.3') was successfully installed.
Removing intermediate container 966fb4ffb6c4
 ---> e88c1a463716
Step 8/8 : ENV PATH $PATH:/root/.dotnet/tools
 ---> Running in d22adac7792a
Removing intermediate container d22adac7792a
 ---> cad2f6cc8e05
Successfully built cad2f6cc8e05
Successfully tagged test:latest
```

通った。動作させてみる。

```sh
$ docker run --rm -it test   
# env
DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false
HOSTNAME=0b10f879c800
DOTNET_USE_POLLING_FILE_WATCHER=true
SHLVL=1
HOME=/root
DOTNET_RUNNING_IN_CONTAINER=true
POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
TERM=xterm
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.dotnet/tools
LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8
ASPNETCORE_URLS=
PWD=/
NUGET_XMLDOC_MODE=skip
```

パスが通っている。 `dotnet ef` を実行してみる。

```sh
# dotnet ef

                     _/\__       
               ---==/    \\      
         ___  ___   |.    \|\    
        | __|| __|  |  )   \\\   
        | _| | _|   \_/ |  //|\\ 
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 3.1.6

Usage: dotnet ef [options] [command]

Options:
  --version        Show version information
  -h|--help        Show help information
  -v|--verbose     Show verbose output.
  --no-color       Don't colorize output.
  --prefix-output  Prefix output with level.

Commands:
  database    Commands to manage the database.
  dbcontext   Commands to manage DbContext types.
  migrations  Commands to manage migrations.

Use "dotnet ef [command] --help" for more information about a command.
```

通ったっぽい。ここまでを shell scirpt にまとめる。

```sh
#!/bin/sh

#get absolute path of directory contained this script
SCRIPT_DIR=$(cd $(dirname $0); pwd)

DOCKERFILE_DIR=$SCRIPT_DIR/Dockerfiles

if [ ! -e $DOCKERFILE_DIR ]; then
  mkdir $DOCKERFILE_DIR
fi

ORIGIN_DOCKERFILE=$SCRIPT_DIR/dotnet-docker/src/sdk/3.1/alpine3.12/amd64/Dockerfile

if [ -e $DOCKERFILE_DIR/Dockerfile_RizorPage ]; then
  rm $DOCKERFILE_DIR/Dockerfile_RizorPage
fi

cp $ORIGIN_DOCKERFILE $DOCKERFILE_DIR/Dockerfile_RizorPage

cat << 'EOF__' >> $DOCKERFILE_DIR/Dockerfile_RizorPage

RUN dotnet tool install --global dotnet-ef \
 && dotnet tool install --global dotnet-aspnet-codegenerator

ENV PATH $PATH:/root/.dotnet/tools

EOF__
```

動作確認できたので、コンテナイメージを作るスクリプト、
コンテナを動作させるスクリプトをこの _Dockerfile_RizorPage_ に合わせて変更する。

```sh
$ vimr create_RizorPage_image.sh
```

```sh
#!/bin/sh

docker build --pull -t dotnetsdk3.1 -f ./Dockerfiles/Dockerfile_RizorPage ./Dockerfiles
```

実行してみる。

```sh
$ ./create_RizorPage_image.sh   
Sending build context to Docker daemon  4.608kB
Step 1/8 : ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
Step 2/8 : FROM $REPO:3.1-alpine3.12
3.1-alpine3.12: Pulling from dotnet/core/aspnet
df20fa9351a1: Pull complete 
ac47820464eb: Pull complete 
c991f9f4493d: Pull complete 
bb9fc6048a9d: Pull complete 
Digest: sha256:a1a68872445e04f3ff3128b15a6dbf3a935630607d91f5830433c14a7b51560a
Status: Downloaded newer image for mcr.microsoft.com/dotnet/core/aspnet:3.1-alpine3.12
 ---> e4ede6c1c5d4
Step 3/8 : ENV     ASPNETCORE_URLS=     DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false     DOTNET_USE_POLLING_FILE_WATCHER=true     LC_ALL=en_US.UTF-8     LANG=en_US.UTF-8     NUGET_XMLDOC_MODE=skip     POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
~~~(省略)~~~
Step 8/8 : ENV PATH $PATH:/root/.dotnet/tools
 ---> Running in 717b87c6c9aa
Removing intermediate container 717b87c6c9aa
 ---> 9d9705fc0f1e
Successfully built 9d9705fc0f1e
Successfully tagged dotnetsdk3.1:latest
```

動作した。 `docker images` で確認する。

```sh
$ docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
dotnetsdk3.1                           latest              9d9705fc0f1e        2 minutes ago       427MB
mcr.microsoft.com/dotnet/core/aspnet   3.1-alpine3.12      e4ede6c1c5d4        2 weeks ago         105MB
```

できている。あとはディレクトリをマウントして実行してみる。
ここは以前と変更せずによさそうだ。コピーして使い回す。

```sh
$ cp run_SDK_container.sh run_RizorPage_container.sh
```

動かしてみる。 `env` で反映されているかも確認する。

```sh
$ ./run_RizorPage_container.sh 
# env
DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false
HOSTNAME=5c7acedd6c64
DOTNET_USE_POLLING_FILE_WATCHER=true
SHLVL=1
HOME=/root
DOTNET_RUNNING_IN_CONTAINER=true
POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
TERM=xterm
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.dotnet/tools
LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8
ASPNETCORE_URLS=
PWD=/
NUGET_XMLDOC_MODE=skip
```

問題なさそうだ。