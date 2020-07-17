---
layout : post
title  : "ASP.NET Core SDK コンテナでWebアプリを動かす"
date   : 2020/07/17
lastchange : 2020-07-17 17:40:56
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## ASP.NET Core コンテナで WebApp を作り、コンテナ外からアクセスする

[ASP.NET Core SDK コンテナを動かす]({{site.github.url}}{% link _docs/Docker/docker-asp.net-core-sdk.md %})
でコンテナ内にソーステンプレートを作成することができるところまでは確認できた。
今回はテンプレートから Web アプリを動かし、コンテナ外からアクセスしてみる。

今回はまだ外部のドライブをマウントせず、内部で変更をしてみるだけ。
基本的なコマンドの説明は

* [Dockerに慣れる : コンテナを操作する]({{site.github.url}}{% link _docs/Docker/basic-container.md %})
* [Dockerに慣れる : 一時的にコンテナ内の情報を変更する]({{site.github.url}}{% link _docs/Docker/basic-volatility-change.md %})

にまとめた。

参考:

* [ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/ "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
* [docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita](https://qiita.com/amuyikam/items/01a8c16e3ddbcc734a46 "docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita")
* [MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")




## 外部からアクセスできるようにポート引数を加える

コンテナ内部で作成した WebApp に外部からアクセスできるように、ポート設定の引数を設定する。
転送するポートは今回のチュートリアルの設定を使う。

{% capture text %}
Once the command completes, browse to <http://localhost:5000>
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/run "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

_5000_ を利用するらしいのでコンテナ外の _5000_ とコンテナ内の _5000_ をつなげることにした。
よって、

```sh
$ docker run --rm -it -p 5000:5000 dotnetsdk3.1alpine3.12 
/ #
```

このように実行した。


## チュートリアルに沿ってアプリを作成する

前回同様に _/home_ 以下にディレクトリを作ってその中でテンプレートからプロジェクトを生成する。
今回は `webApp` というテンプレートを利用するらしい。

{% capture text %}
## Create your app

In your terminal, run the following commands:

```sh
dotnet new webApp -o myWebApp --no-https
cd myWebApp
```
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/create "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この引数に関しての解説は以下


{% capture text %}
### What do these commands mean?

The `dotnet new` command creates a new application.

* The `webApp` parameter selects what template to use when creating your app.
* The `-o` parameter creates a directory named `myWebApp` where your app is stored.
* The `--no-https` flag specifies not to enable HTTPS.

The `cd myWebApp` command puts you into the newly created app directory.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/create "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`webApp`
: webApp というテンプレートを利用する

`-o myWebApp` 
: _myWebApp_ というディレクトリをコマンドを実行したディレクトリの下に作成し、そこにプロジェクトを生成する

`--no-https`
: https 機能を有効化しない

また、生成されたプロジェクトに関しても簡単な解説が入っている。

{% capture text %}

### What files were created?

Several files were created in the `myWebApp` directory, to give you a simple web application that is ready to run.

* `Startup.cs` contains all the settings and configurations.
* The `myWebApp/Pages` directory contains some example web pages for the application.
* `myWebApp.csproj` defines what libraries are referenced etc.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/create "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`Startup.cs`
: プロジェクトの全ての設定が入っている

`myWebApp/Pages`
: アプリケーション向けにいくつかのサンプルページが入っている

`myWebApp.csproj`
: どのライブラリを参照するか定義している

ではこのコードを内部で実行してみる。
既にコンテナは動作しているので、

```sh
# cd home/
/home # dotnet new webApp -o myWebApp --no-https
Getting ready...
The template "ASP.NET Core Web App" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/aspnetcore/3.1-third-party-notices for details.

Processing post-creation actions...
Running 'dotnet restore' on myWebApp/myWebApp.csproj...
  Determining projects to restore...
  Restored /home/myWebApp/myWebApp.csproj (in 150 ms).

Restore succeeded.
```

テンプレートの生成ができた。 _myWebApp_ に移動する

```sh
/home # cd myWebApp
/home/myWebApp #
```

## アプリを実行してアクセスしてみる

{% capture text %}

In your terminal, run the following command:

```sh
dotnet run
```

Once the command completes, browse to <http://localhost:5000>

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/run "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

動作させてみる

```sh
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
warn: Microsoft.AspNetCore.Server.Kestrel[0]
      Unable to bind to http://localhost:5000 on the IPv6 loopback interface: 'Address not available'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /home/myWebApp
```

<http://localhost:5000> にアクセスしてみるが、繋がらない。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/asp.net-core-sdk-webapp/01_empty_reply.png %}{% endcapture %}
{% capture caption %}
接続エラーになる。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


{% capture text %}

念の為、ホストマシンからcurlで確認してみましょう。
`Empty reply from server` エラーです。

```sh
$ curl 'http://localhost:8000/'
curl: (52) Empty reply from server
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita](https://qiita.com/amuyikam/items/01a8c16e3ddbcc734a46 "docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


やってみると確かに Empty reply from server エラーになる。

```sh
$ curl localhost:5000
curl: (52) Empty reply from server
```

このページに原因らしきものと解決策も載っていた。



{% capture text %}

## tl;dr

(問題)
: webアプリをdockerで立ててアクセスしたら `ERR_EMPTY_RESPONSE` エラーになった

(原因)
: containerの外からリクエストが来るのにアプリがlocalhostでLISTENしている

(解決)
: アプリの設定を0.0.0.0でLISTENするよう変更する

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita](https://qiita.com/amuyikam/items/01a8c16e3ddbcc734a46 "docker上のアプリにlocalhostでアクセスしたらERR_EMPTY_RESPONSEが出る - Qiita")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

では **0.0.0.0** からのリクエストを処理するように変更すればよさそうだ。

{% capture text %}

# ホスト側からアクセス可能にする為”微調整”

これからコンテナ側でビルド＆実行するWebアプリケーションを、
ホスト側（つまり外部マシン）からアクセス可能とする修正を行います。
`dotnet new -t Web` で自動生成されたソース中の「Program.cs」ファイルに「.UseUrls(“<http://0.0.0.0:5000>”)」の1行を追記します。

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseKestrel()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .UseUrls("http://0.0.0.0:5000")  // 追記！！！
                .Build();

            host.Run();
        }
    }
}
```

# dotnet restore / run

`dotnet restore` と `dotnet run` コマンドを実行します。

```sh
dotnet restore
dotnet run
```

restoreは、このアプリケーションに必要な依存関係ファイルを、プロジェクト内にリストアします。
runは、このアプリケーションをビルド＆実行します（ビルドだけ行う場合は dotnet build とします）。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記](https://ryuichi111std.hatenablog.com/entry/2016/11/07/020326 "MS公式Dockerイメージを使って.NET Core開発を行う（Mac） - ryuichi111stdの技術日記")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これをやってみる。まずは _Program.cs_ を編集する。

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

namespace myWebApp
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
                    webBuilder
		    .UseStartup<Startup>()
		    .UseUrls("http://0.0.0.0:5000"); //追加
                });
    }
}
```

そしたら `dotnet restore` と `dotnet run` をやってみる。

```sh
# dotnet restore
  Determining projects to restore...
  All projects are up-to-date for restore.
/home/myWebApp # dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://0.0.0.0:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /home/myWebApp
```

受け付けるアドレスが <http://localhost:5000> から <http://0.0.0.0:5000> に変化している。
いけそうな気がする。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/asp.net-core-sdk-webapp/02_webapp.png %}{% endcapture %}
{% capture caption %}
接続できた！
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}
