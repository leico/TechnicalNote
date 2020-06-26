---
layout : post
title  : "ASP.NET Core SDK コンテナを動かす"
date   : 2020/06/26
lastchange : 2020-06-26 18:40:25
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
---

## ASP.NET Core 開発仮想環境を整備する

ASP.NET の開発をする際、公式を確認すると .NET SDK のインストールを促される。

{% capture text %}

## 必須コンポーネント

* [Visual Studio for Mac バージョン 8.4 以降](https://visualstudio.microsoft.com/ja/vs/mac/)
* [.NET Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
["チュートリアル: ASP.NET Core の Razor Pages の概要" \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-3.1&tabs=visual-studio-mac ""チュートリアル: ASP.NET Core の Razor Pages の概要" \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

一方で、 .NET Core SDK の Docker も配布されている。


{% capture text %}

* `dotnet/core/sdk`

サンプルでは、アプリをビルドするためにこのイメージを使用します。 
イメージには、コマンド ライン ツール (CLI) が組み込まれた .NET Core SDK が含まれています。
イメージはローカル開発、デバッグ、および単体テスト用に最適化されています。
開発とコンパイルのためにツールがインストールされているため、これは比較的大きなイメージになっています。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Core 向けの Docker イメージ \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-3.1 "ASP.NET Core 向けの Docker イメージ \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

なので、 .NET Core SDK の Docker を利用して開発用仮想環境を立ち上げてみる。

参考:

* ["チュートリアル: ASP.NET Core の Razor Pages の概要" \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-3.1&tabs=visual-studio-mac ""チュートリアル: ASP.NET Core の Razor Pages の概要" \| Microsoft Docs")
* [ASP.NET Core 向けの Docker イメージ \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-3.1 "ASP.NET Core 向けの Docker イメージ \| Microsoft Docs")
* [MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")
* [超軽量なAlpine Linuxについて調べた - Qiita](https://qiita.com/ryuichi1208/items/6020cfabc92bd8153654 "超軽量なAlpine Linuxについて調べた - Qiita")
* [dotnet-docker/Dockerfile at master · dotnet/dotnet-docker](https://github.com/dotnet/dotnet-docker/blob/master/src/sdk/3.1/alpine3.12/amd64/Dockerfile#L1-L2 "dotnet-docker/Dockerfile at master · dotnet/dotnet-docker")


## コンテナイメージを作成する

{% capture text %}

## Dockerfileを作成

「1.0.0-preview2.1-sdk」の対象URL（GitHubへのリンク）にアクセスします（下図）。

___

これは Dockerfile となっています（https://github.com/dotnet/dotnet-docker/blob/master/1.0.0-preview2.1/debian/sdk/Dockerfile）。

___

これをそのまま「/Users/daigo/docker/Dockerfile」として保存します。

**↑(2017.2.16追記)上記リンクは死んでるみたい・・・適当な別バージョンで実施してくださいm( )m**

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この _Dockerfile_ を公式リポジトリから探し出す。公式リポジトリは

{% capture text %}

## サンプル アプリ をダウンロードする

.NET Core の Docker リポジトリを複製して、サンプルをダウンロードします。

```sh
git clone https://github.com/dotnet/dotnet-docker
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Core 向けの Docker イメージ \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-3.1 "ASP.NET Core 向けの Docker イメージ \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このリポジトリになる。捜索したところこれらしいというのが見つかった。

<https://github.com/dotnet/dotnet-docker/tree/master/src/sdk/3.1/alpine3.12/amd64>

上記URL以外にも、さまざまなバージョンが存在しているのでまとめてみる。

### SDK のバージョンについて

まず SDK のバージョンが執筆現在

* 2.1
* 3.1
* 5.0

から選択できる。

<https://github.com/dotnet/dotnet-docker/tree/master/src/sdk>


### SDK のベースOSについて

SDK のバージョンのいずれかを選択すると、対応するベース OS のディレクトリが表示される。
執筆現在

* alpine3.11
* alpine3.12
* bionic
* buster
* focal
* nanoserver-1809
* nanoserver-1903
* nanoserver-1909
* nanoserver-2004

という種類があった。

<https://github.com/dotnet/dotnet-docker/tree/master/src/sdk/3.1>

この中で **Alpine 3.12** を選択した。Alpine とは



{% capture text %}

## Alpine Linuxとは

組み込み系でよく使われているBusyBoxとmuslをベースにしたLinuxディストリビューション。
BusyBoxについては下記がまとまっていて見やすかったです。

<http://monoist.atmarkit.co.jp/mn/articles/0802/04/news114.html>

Docker界隈でよく見かける言葉ですが特にDocker用とかそういったものでは無い。

BusyBox + パッケージマネージャ(apk)がAlpineLinuxの概要とのことです。

公式サイトは下記です。

<https://alpinelinux.org/>
(GitHub) <https://github.com/alpinelinux>

Alpineの特徴に軽量とありますがどれくらい軽量化というと下記比較を見れば一目瞭然

  CentOS 約4GB
  Ubuntu 約700MB
  Alpine 約100MB

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[超軽量なAlpine Linuxについて調べた - Qiita](https://qiita.com/ryuichi1208/items/6020cfabc92bd8153654 "超軽量なAlpine Linuxについて調べた - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


Alpine Linux をベース OS にしたコンテナイメージということらしい。ということで内部の _Dockerfile_ を確認してみる。

{% capture text %}

```dockerfile
ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
FROM $REPO:3.1-alpine3.12
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[dotnet-docker/Dockerfile at master · dotnet/dotnet-docker](https://github.com/dotnet/dotnet-docker/blob/master/src/sdk/3.1/alpine3.12/amd64/Dockerfile#L1-L2 "dotnet-docker/Dockerfile at master · dotnet/dotnet-docker")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

当たりっぽいかな。ということでこれを利用することにする。



{% capture text %}

## Dockerイメージのビルド

ターミナルで以下のコマンドを実行します。先程作成したDockerfileを元にして、
Dockerイメージをビルドします（ここではイメージ名を dotnet100pv21 としています）。

```sh
docker build -t dotnet100pv21 .
```

イメージのダウンロードとビルドに数分程度かかると思います。
処理が完了したら以下のコマンドを実行してみましょう。dotnet100pv21という名称のイメージが作成されていることが確認できます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



今回はコンテナイメージ名を `dotnetsdk3.1alpine3.12` とした。

```sh
 $ docker build --pull -t dotnetsdk3.1alpine3.12 .
 Sending build context to Docker daemon  4.608kB
Step 1/6 : ARG REPO=mcr.microsoft.com/dotnet/core/aspnet
Step 2/6 : FROM $REPO:3.1-alpine3.12
3.1-alpine3.12: Pulling from dotnet/core/aspnet
df20fa9351a1: Pull complete 
ca1688c5bf4a: Pull complete 
777cb091de55: Pull complete 
bce8880f9e16: Pull complete 
Digest: sha256:a0944fc49430cb7b3980055dfdefcbe5b36fa5c21d8db6e68ba36cf57f54593b
Status: Downloaded newer image for mcr.microsoft.com/dotnet/core/aspnet:3.1-alpine3.12
 ---> 4213a3f7e30c
Step 3/6 : ENV     ASPNETCORE_URLS=     DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false     DOTNET_USE_POLLING_FILE_WATCHER=true     LC_ALL=en_US.UTF-8     LANG=en_US.UTF-8     NUGET_XMLDOC_MODE=skip     POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-Alpine-3.12
 ---> Running in 3ef4d5e6d5ef
Removing intermediate container 3ef4d5e6d5ef
 ---> 5853635f8124
Step 4/6 : RUN apk add --no-cache icu-libs
 ---> Running in 20c558669fa0
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/1) Installing icu-libs (67.1-r0)
OK: 42 MiB in 24 packages
Removing intermediate container 20c558669fa0
 ---> f31726e56cba
Step 5/6 : RUN dotnet_sdk_version=3.1.301     && wget -O dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Sdk/$dotnet_sdk_version/dotnet-sdk-$dotnet_sdk_version-linux-musl-x64.tar.gz     && dotnet_sha512='f1631569991b448ab9814fe1150609136f428d686d67f93e23b203163dc743de9b0c061bd5dd15f383c832a143ed9e2f7e70d4d417f75c6812d4ff09efb82188'     && echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c -     && mkdir -p /usr/share/dotnet     && tar -C /usr/share/dotnet -oxzf dotnet.tar.gz ./packs ./sdk ./templates ./LICENSE.txt ./ThirdPartyNotices.txt     && rm dotnet.tar.gz     && dotnet help
 ---> Running in d78c3259bbca
Connecting to dotnetcli.azureedge.net (117.18.232.200:443)
saving to 'dotnet.tar.gz'
dotnet.tar.gz          6% |**                              | 7391k  0:00:14 ETA
dotnet.tar.gz         22% |*******                         | 26.0M  0:00:06 ETA
dotnet.tar.gz         39% |************                    | 45.1M  0:00:04 ETA
dotnet.tar.gz         58% |******************              | 67.0M  0:00:02 ETA
dotnet.tar.gz         78% |*************************       | 89.8M  0:00:01 ETA
dotnet.tar.gz         98% |******************************* |  111M  0:00:00 ETA
dotnet.tar.gz        100% |********************************|  113M  0:00:00 ETA
'dotnet.tar.gz' saved
dotnet.tar.gz: OK

Welcome to .NET Core 3.1!
---------------------
SDK Version: 3.1.301

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
.NET Core SDK (3.1.301)
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
Removing intermediate container d78c3259bbca
 ---> 555a143d8e05
Step 6/6 : RUN powershell_version=7.0.1     && wget -O PowerShell.Linux.Alpine.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.Alpine.$powershell_version.nupkg     && powershell_sha512='a2390780d098e686c9e43a3ade215240ac7699904807cebcdb00804248b88f1afb8592aca29d28d3a9c9294534016a6d5c4014e7d0f2c2865518c32be02b8368'     && echo "$powershell_sha512  PowerShell.Linux.Alpine.$powershell_version.nupkg" | sha512sum -c -     && mkdir -p /usr/share/powershell     && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.Alpine     && dotnet nuget locals all --clear     && rm PowerShell.Linux.Alpine.$powershell_version.nupkg     && chmod 755 /usr/share/powershell/pwsh     && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh     && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm     && apk add --no-cache ncurses-terminfo-base
 ---> Running in 81388e3df142
Connecting to pwshtool.blob.core.windows.net (52.191.176.36:443)
saving to 'PowerShell.Linux.Alpine.7.0.1.nupkg'
PowerShell.Linux.Alp   3% |                                |  415k  0:00:31 ETA
PowerShell.Linux.Alp  19% |******                          | 2687k  0:00:08 ETA
PowerShell.Linux.Alp  23% |*******                         | 3167k  0:00:09 ETA
PowerShell.Linux.Alp  28% |*********                       | 3887k  0:00:09 ETA
PowerShell.Linux.Alp  34% |***********                     | 4656k  0:00:09 ETA
PowerShell.Linux.Alp  40% |************                    | 5472k  0:00:08 ETA
PowerShell.Linux.Alp  47% |***************                 | 6496k  0:00:07 ETA
PowerShell.Linux.Alp  57% |******************              | 7840k  0:00:05 ETA
PowerShell.Linux.Alp  67% |*********************           | 9184k  0:00:04 ETA
PowerShell.Linux.Alp  77% |************************        | 10.2M  0:00:02 ETA
PowerShell.Linux.Alp  80% |*************************       | 10.6M  0:00:02 ETA
PowerShell.Linux.Alp  81% |**************************      | 10.8M  0:00:02 ETA
PowerShell.Linux.Alp  84% |**************************      | 11.1M  0:00:02 ETA
PowerShell.Linux.Alp  87% |****************************    | 11.5M  0:00:01 ETA
PowerShell.Linux.Alp  91% |*****************************   | 12.1M  0:00:01 ETA
PowerShell.Linux.Alp  94% |******************************  | 12.4M  0:00:00 ETA
PowerShell.Linux.Alp  98% |******************************* | 12.9M  0:00:00 ETA
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
Removing intermediate container 81388e3df142
 ---> 25db00bce912
Successfully built 25db00bce912
Successfully tagged dotnetsdk3.1alpine3.12:latest
```

できたらしいので `docker images` で確認してみる。

```sh
 $ docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
dotnetsdk3.1alpine3.12                 latest              25db00bce912        9 seconds ago       422MB
mcr.microsoft.com/dotnet/core/aspnet   3.1-alpine3.12      4213a3f7e30c        8 days ago          105MB
```

できたらしい。



## コンテナイメージを動作させる


{% capture text %}

## Dockerコンテナの起動

ではコンテナを起動します。
ターミナルで以下のコマンドを実行します。

```sh
docker run -i -t -v /Users/daigo/Docker/Projects:/Home/Projects -p 5000:5000  dotnet100pv21
```

主要なコマンドオプションの意味は以下の通りです。

「-v」オプション
: ”ホスト側（Mac）の /Users/daigo/Docker/Projectsディレクトリ を コンテナ側の /Home/Projectsディレクトリ として公開（マッピング）”します。

「-p」オプション
: ”ホスト側（Mac）の 5000番ポート を コンテナ側の 5000番ポート にマッピング”します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記ページではいきなりディレクトリなどをマウントしているが、
とりあえず動作させて `dotnet` コマンドが動作するか確認する。

まずはコンテナイメージに入れるか確認する。

```sh
$ docker run --rm -it dotnetsdk3.1alpine3.12
/ # 
```

入れた。参考サイトでは _/Home/Projects_ にホストのディレクトリをマウントしているので、 _/home_ 内を確認してみる。

```sh
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # ls home/
/ #
```

なんもなかった。このディレクトリに _test_ ディレクトリを作り、その中で `docker new` を試してみる。

```sh
/ # cd home/
/home # mkdir test
/home # cd test
```

_/home/test_ に移動した。 `dotnet new` のヘルプを確認してみる。

```sh
/home/test # dotnet new --help
Getting ready...
Usage: new [options]

Options:
  -h, --help          Displays help for this command.
  -l, --list          Lists templates containing the specified name. If no name is specified, lists all templates.
  -n, --name          The name for the output being created. If no name is specified, the name of the current directory is used.
  -o, --output        Location to place the generated output.
  -i, --install       Installs a source or a template pack.
  -u, --uninstall     Uninstalls a source or a template pack.
  --nuget-source      Specifies a NuGet source to use during install.
  --type              Filters templates based on available types. Predefined values are "project", "item" or "other".
  --dry-run           Displays a summary of what would happen if the given command line were run if it would result in a template creation.
  --force             Forces content to be generated even if it would change existing files.
  -lang, --language   Filters templates based on language and specifies the language of the template to create.
  --update-check      Check the currently installed template packs for updates.
  --update-apply      Check the currently installed template packs for update, and install the updates.


Templates                                         Short Name               Language          Tags                                 
----------------------------------------------------------------------------------------------------------------------------------
Console Application                               console                  [C#], F#, VB      Common/Console                       
Class library                                     classlib                 [C#], F#, VB      Common/Library                       
WPF Application                                   wpf                      [C#]              Common/WPF                           
WPF Class library                                 wpflib                   [C#]              Common/WPF                           
WPF Custom Control Library                        wpfcustomcontrollib      [C#]              Common/WPF                           
WPF User Control Library                          wpfusercontrollib        [C#]              Common/WPF                           
Windows Forms (WinForms) Application              winforms                 [C#]              Common/WinForms                      
Windows Forms (WinForms) Class library            winformslib              [C#]              Common/WinForms                      
Worker Service                                    worker                   [C#]              Common/Worker/Web                    
Unit Test Project                                 mstest                   [C#], F#, VB      Test/MSTest                          
NUnit 3 Test Project                              nunit                    [C#], F#, VB      Test/NUnit                           
NUnit 3 Test Item                                 nunit-test               [C#], F#, VB      Test/NUnit                           
xUnit Test Project                                xunit                    [C#], F#, VB      Test/xUnit                           
Razor Component                                   razorcomponent           [C#]              Web/ASP.NET                          
Razor Page                                        page                     [C#]              Web/ASP.NET                          
MVC ViewImports                                   viewimports              [C#]              Web/ASP.NET                          
MVC ViewStart                                     viewstart                [C#]              Web/ASP.NET                          
Blazor Server App                                 blazorserver             [C#]              Web/Blazor                           
Blazor WebAssembly App                            blazorwasm               [C#]              Web/Blazor/WebAssembly               
ASP.NET Core Empty                                web                      [C#], F#          Web/Empty                            
ASP.NET Core Web App (Model-View-Controller)      mvc                      [C#], F#          Web/MVC                              
ASP.NET Core Web App                              webapp                   [C#]              Web/MVC/Razor Pages                  
ASP.NET Core with Angular                         angular                  [C#]              Web/MVC/SPA                          
ASP.NET Core with React.js                        react                    [C#]              Web/MVC/SPA                          
ASP.NET Core with React.js and Redux              reactredux               [C#]              Web/MVC/SPA                          
Razor Class Library                               razorclasslib            [C#]              Web/Razor/Library/Razor Class Library
ASP.NET Core Web API                              webapi                   [C#], F#          Web/WebAPI                           
ASP.NET Core gRPC Service                         grpc                     [C#]              Web/gRPC                             
dotnet gitignore file                             gitignore                                  Config                               
global.json file                                  globaljson                                 Config                               
NuGet Config                                      nugetconfig                                Config                               
Dotnet local tool manifest file                   tool-manifest                              Config                               
Web Config                                        webconfig                                  Config                               
Solution File                                     sln                                        Solution                             
Protocol Buffer File                              proto                                      Web/gRPC                             

Examples:
    dotnet new mvc --auth Individual
    dotnet new webapp --auth Individual
    dotnet new --help
```

ASP.NET Core をやりたいので、テンプレートから `dotnet new web` を選択して実行してみる。

```sh
/home/test # dotnet new web
The template "ASP.NET Core Empty" was created successfully.

Processing post-creation actions...
Running 'dotnet restore' on /home/test/test.csproj...
  Determining projects to restore...
  Restored /home/test/test.csproj (in 212 ms).

Restore succeeded.
```

できたらしい。 内部を確認する。

```sh
/home/test # ls
Program.cs                    Startup.cs                    appsettings.json              test.csproj
Properties                    appsettings.Development.json  obj
```

どうやらちゃんといけたらしい。 ホスト OS のディレクトリをマウントしていれば
ホスト OS 側にプロジェクトを作成して開発ができるようになりそうだ。