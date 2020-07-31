---
layout : post
title  : "Razor Page プロジェクトを作成する"
date   : 2020-07-23
lastchange : 2020-07-27 15:08:13
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## Microsoft 公式のチュートリアルプロジェクトを作成する

[Docker 内にマウントするディレクトリ構成を変更する]({{site.github.url}}{% link _docs/AspDotNetCore/prepare-directory.md %})
で次のチュートリアルの準備ができたので、今回からチュートリアルを進めていく。

参考:

* [チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-3.1&tabs=visual-studio-code "チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs")


## プロジェクトを作成する

{% capture text %}

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal) を開きます。
* プロジェクトを格納するディレクトリ (cd) に変更します。
* 次のコマンドを実行します。
    ```sh
    dotnet new webapp -o RazorPagesMovie
    code -r RazorPagesMovie
    ```
  * `dotnet new` コマンド : _RazorPagesMovie_ フォルダーに新しい Razor Pages プロジェクトが作成されます。
  * `code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で _RazorPagesMovie_ フォルダーが開きます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-3.1&tabs=visual-studio-code "チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

[Docker 内にマウントするディレクトリ構成を変更する]({{site.github.url}}{% link _docs/AspDotNetCore/prepare-directory.md %})
で作成した _Projects_ ディレクトリでやってみる。

まずは dotnet SDK コンテナを立ち上げる。

```sh
$ ./run_SDK_container.sh 
# ls
Projects  etc       media     proc      sbin      tmp
bin       home      mnt       root      srv       usr
dev       lib       opt       run       sys       var
# cd Projects/
# ls
WebApp
```

ホストの _Projects_ ディレクトリがマウントされていて、その中間で移動した。
ここで _RazorPagesMovie_ を作成する。

```sh
# dotnet new webapp -o RazorPagesMovie
Getting ready...
The template "ASP.NET Core Web App" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/aspnetcore/3.1-third-party-notices for details.

Processing post-creation actions...
Running 'dotnet restore' on RazorPagesMovie/RazorPagesMovie.csproj...
  Determining projects to restore...
  Restored /Projects/RazorPagesMovie/RazorPagesMovie.csproj (in 131 ms).

Restore succeeded.

# ls
RazorPagesMovie  WebApp
```

作成できた。とりあえず実行してみる。

```sh
# cd RazorPagesMovie/
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
warn: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[35]
      No XML encryptor configured. Key {4d734ed9-6c6d-45be-850c-6827b7f10d84} may be persisted to storage in unencrypted form.
warn: Microsoft.AspNetCore.Server.Kestrel[0]
      Unable to bind to https://localhost:5001 on the IPv6 loopback interface: 'Address not available'.
warn: Microsoft.AspNetCore.Server.Kestrel[0]
      Unable to bind to http://localhost:5000 on the IPv6 loopback interface: 'Address not available'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Projects/RazorPagesMovie
```

動いたが、 URL を変更しないとホスト側からアクセスできない。中止して変更する。

ホスト側から _Projects/RazorPagesMovie_ にアクセスする。

```sh
$ cd Projects
$ ls
RazorPagesMovie	WebApp
$ cd RazorPagesMovie
```

_Program.cs_ を編集する。

```sh
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

namespace RazorPagesMovie
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
                    .UseUrls("http://0.0.0.0:5000"); // 追加
                });
    }
}
```

実行してホスト側から接続して確認してみる。
コンテナ側で動作させる。

```sh
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://0.0.0.0:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Projects/RazorPagesMovie
warn: Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionMiddleware[3]
      Failed to determine the https port for redirect.
```

ホスト側から <http://localhost:5000> にアクセスする。

{% capture url %}{{site.github.url}}{% link _docs/AspDotNetCore/images/create-project/01_welcome.png %}{% endcapture %}
{% capture caption %}
接続確認できた。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


## https でアクセスできるようにする。

URLを <https://localhost:5001> でアクセスできるようにしてみる。
ホスト側でもう一度 _Program.cs_ を変更する。

```sh
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

namespace RazorPagesMovie
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
                    .UseUrls(new [] {                 //変更
                          "http://0.0.0.0:5000"
                        , "https://0.0.0.0:5001" 
                     });
                });
    }
}
```

実行してみる。

```sh
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://0.0.0.0:5000
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://0.0.0.0:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Projects/RazorPagesMovie
```

URLが追加されている。アクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/AspDotNetCore/images/create-project/02_warning.png %}{% endcapture %}
{% capture caption %}
証明書の警告が出た。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

Firefox だと <http://0.0.0.0:5000> へアクセスしてもリダイレクトされ、上の警告画面が表示される。

特に危険な場所に向かっているわけではないので、 `危険性を承知で続行` ボタンを押す。

{% capture url %}{{site.github.url}}{% link _docs/AspDotNetCore/images/create-project/03_https.png %}{% endcapture %}
{% capture caption %}
とりあえず表示できた。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

以下、このプロジェクトで生成されたファイルの情報が書かれていたので引用する。


{% capture text %}

## プロジェクト ファイルを確認する

以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。

### Pages フォルダー

Razor ページとサポート ファイルが含まれます。 各 Razor ページは、次のファイルのペアとなります。

* _.cshtml_ ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。
* _.cshtml.cs_ ファイル: ページ イベントを処理する C# コードが保存されます。

サポート ファイルには、アンダー スコアで始まる名前が付けられます。
たとえば、 _\_Layout.cshtml_ ファイルでは、すべてのページに共通の UI 要素が構成されます。
このファイルでは、ページの上部に表示されるナビゲーション メニューと、
ページの下部に表示される著作権の通知が設定されます。 詳細については、「
[ASP.NET Core でのレイアウト](https://docs.microsoft.com/ja-jp/aspnet/core/mvc/views/layout?view=aspnetcore-3.1)
」を参照してください。

### wwwroot フォルダー

HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。
詳細については、「
[ASP.NET Core の静的ファイル](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/static-files?view=aspnetcore-3.1)
」を参照してください。

### appSettings.json

接続文字列などの構成データが保存されます。 詳細については、「
[ASP.NET Core の構成](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1)
」を参照してください。

### Program.cs

プログラムのエントリ ポイントが保存されます。 詳細については、「
[.NET での汎用ホスト](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.1)
」を参照してください。

### Startup.cs

アプリの動作を構成するコードが含まれています。 詳細については、「
[ASP.NET Core でのアプリケーションのスタートアップ](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/startup?view=aspnetcore-3.1)
」を参照してください。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-3.1&tabs=visual-studio-code "チュートリアル: ASP.NET Core の Razor Pages の概要 \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}
