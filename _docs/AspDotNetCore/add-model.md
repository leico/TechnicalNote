---
layout : post
title  : "Model を追加する"
date   : 2020-07-30
lastchange : 2020-07-30 20:30:42
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## モデルを追加する

前回 [Model を追加するためのセットアップ]({{site.github.url}}{% link _docs/AspDotNetCore/add-model-setup.md %}) 
では必要なパッケージのインストールを行った。今回は実際にモデルを作成して動かしてみる。

参考 :

* [パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")


## モデルを作成する

{% capture text %}

* _Models_ という名前のフォルダーを追加します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。ホスト側で編集する。

```sh
$ cd Projects/RazorPagesMovie 
$ ls
Pages				Startup.cs			obj
Program.cs			appsettings.Development.json	wwwroot
Properties			appsettings.json
RazorPagesMovie.csproj		bin
$ mkdir Models
$ ls
Models				RazorPagesMovie.csproj		bin
Pages				Startup.cs			obj
Program.cs			appsettings.Development.json	wwwroot
Properties			appsettings.json
```

できた。


{% capture text %}

* _Movie.cs_ という名前の Models フォルダーにクラスを追加します。

Movie クラスに次のプロパティを追加します。

```cs
using System;
using System.ComponentModel.DataAnnotations;

namespace RazorPagesMovie.Models
{
    public class Movie
    {
        public int ID { get; set; }
        public string Title { get; set; }

        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
```

`Movie` クラスには次が含まれます。

* `ID` フィールドは、データベースで主キー用に必要です。

* `[DataType(DataType.Date)]` : [DataType](https://docs.microsoft.com/ja-jp/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netcore-3.1) 
  属性では、データの型 (Date) を指定します。 この属性を使用する場合:
    * ユーザーは日付フィールドに時刻の情報を入力する必要はありません。
    * 日付のみが表示され、時刻の情報は表示されません。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

_Movies.cs_ ファイルを作成する。

```sh
$ vimr Models/Movie.cs
```

```cs
using System;
using System.ComponentModel.DataAnnotations;

namespace RazorPagesMovie.Models
{
  public class Movie
  {
    public int    ID    { get; set; }
    public string Title { get; set; }

    [DataType(DataType.Date)]
    public DateTime ReleaseDate { get; set; }

    public string  Genre { get; set; }
    public decimal Price { get; set; }
  }
}
```

できたらしい。

{% capture text %}

## データベース コンテキスト クラスの追加

RazorPagesMovie プロジェクトで、 _Data_ という名前の新しいフォルダーを作成します。
次の `RazorPagesMovieContext` クラスを _Data_ フォルダーに追加します。

```cs
using Microsoft.EntityFrameworkCore;

namespace RazorPagesMovie.Data
{
    public class RazorPagesMovieContext : DbContext
    {
        public RazorPagesMovieContext (
            DbContextOptions<RazorPagesMovieContext> options)
            : base(options)
        {
        }

        public DbSet<RazorPagesMovie.Models.Movie> Movie { get; set; }
    }
}
```

上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。
Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、
エンティティはテーブルの行に対応します。 
このコードは、後の手順で依存関係が追加されるまでコンパイルされません。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これも作成する。
ホスト側で _Data_ ディレクトリを作成し、 _Data/RazorPagesMovieContext.cs_ を作成する。

```sh
$ mkdir Data                 
$ vimr Data/RazorPagesMovieContext.cs
```

```cs
using Microsoft.EntityFrameworkCore;

namespace RazorPagesMovie.Data {
  
  public class RazorPagesMovieContext : DbContext
  {
    public RazorPagesMovieContext (
      DbContextOptions<RazorPagesMovieContext> options
    ) : base(options)
    {}

    public DbSet<RazorPagesMovie.Models.Movie> Movie { get; set; }
  }
}
```

できたらしい。

{% capture text %}

## データベース接続文字列の追加

次の強調表示されたコードに示されているように、 _appsettings.json_ ファイルに接続文字列を追加します。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "MovieContext": "Data Source=MvcMovie.db"
  }
}
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これもやる。

```sh
$ vimr appsettings.json 
```

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",          // コンマ追加
  "ConnectionStrings" : {       // 追加
    "MovieContext" : "Data Source=MvcMovie.db" // 追加
  } // 追加
}
```

まだまだ続く。

{% capture text %}

## データベース コンテキストの登録

_Startup.cs_ の先頭に次の `using` ステートメントを追加します。

```cs
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

`Startup.ConfigureServices` で
[依存性の挿入](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1)
コンテナーを使用し、データベース コンテキストを登録します。

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();

    services.AddDbContext<RazorPagesMovieContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("MovieContext")));
}
```

プロジェクトをビルドして、コンパイル エラーがないことを確認します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。

```sh
$ vimr Startup.cs 
```

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

using RazorPagesMovie.Data;          //追加
using Microsoft.EntityFrameworkCore; //追加

namespace RazorPagesMovie
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddRazorPages();

            //追加
            services.AddDbContext<RazorPagesMovieContext>(
              options => options.UseSqlite( Configuration.GetConnectionString("MovieContext") )
            );
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapRazorPages();
            });
        }
    }
}
```

コンパイルしてみる。

```sh
# dotnet build
Microsoft (R) Build Engine version 16.6.0+5ff7b0c9e for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Determining projects to restore...
  All projects are up-to-date for restore.
  RazorPagesMovie -> /Projects/RazorPagesMovie/bin/Debug/netcoreapp3.1/RazorPagesMovie.dll
  RazorPagesMovie -> /Projects/RazorPagesMovie/bin/Debug/netcoreapp3.1/RazorPagesMovie.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:06.11
```

通れば成功。



## ムービーデータのスキャフォールディング

{% capture text %}

## ムービー モデルのスキャフォールディング

このセクションでは、ムービー モデルがスキャフォールディングされます。
つまり、スキャフォールディング ツールにより、ムービー モデルの作成、
読み取り、更新、削除の (CRUD) 操作用のページが生成されます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

スキャフォールディングとはなんだろう。

{% capture text %}

## Scaffolding（スキャフォールディング）とは

**Scaffolding**（スキャフォールディング）とは、データモデルとなる型を元に、
いわゆるCRUD（Create/Read/Upadate/Delete)と呼ばれる追加、読込、変更、削除を行う画面と
そのコードを自動で生成する機能のことです（図1）。
Scaffoldingを和訳すると「足場」という言葉であり、その名のとおり少ない手間でアプリケーションの
「足場」となる大枠の構造を作ることを目的としています。
従って、Scaffoldingは完全なアプリケーションを作るための機能ではない、ということは認識しておく必要があります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET 4.5の「Scaffolding（スキャフォールディング）」機能を試す（前編） (1/5)：CodeZine（コードジン）](https://codezine.jp/article/detail/7491 "ASP.NET 4.5の「Scaffolding（スキャフォールディング）」機能を試す（前編） (1/5)：CodeZine（コードジン）")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

簡単にチェックできるように、自動的にデータベース周辺の操作ページが生成されるらしい。やってみる。

{% capture text %}

* プロジェクト ディレクトリ ( _Program.cs_ 、_Startup.cs_ 、および _.csproj_ ファイルを含むディレクトリ) で
コマンド ウィンドウを開きます。
* スキャフォールディング ツールをインストールします。
    ```sh
    dotnet tool install --global dotnet-aspnet-codegenerator
    ```

Windows の場合
: 次のコマンドを実行します。
    ```sh
    dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
    ```

macOS および Linux の場合
: 次のコマンドを実行します。
    ```sh
    dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
    dotnet tool install --global dotnet-aspnet-codegenerator
    ```

次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。

| パラメーター               | 説明                                                                     |
|----------------------------|--------------------------------------------------------------------------|
| `-m`                         | モデルの名前。                                                           |
| `-dc`                        | 使用する `DbContext` クラス。                                              |
| `-udl`                       | 既定のレイアウトを使用します。                                           |
| `-outDir`                    | ビューを作成するための相対出力フォルダー パス。                          |
| `--referenceScriptLibraries` | [編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。   |

次のように、`h` スイッチを使用して、 `aspnet-codegenerator razorpage` コマンドに関するヘルプを取得します。

```sh
dotnet aspnet-codegenerator razorpage -h
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`dotnet-aspnet-codegenerator` をインストールする

```sh
# dotnet tool install --global dotnet-aspnet-codegenerator
Tool 'dotnet-aspnet-codegenerator' is already installed.
```

インストールされていた。実行するが、上記引用元は余分なものが入っていそうだ。

```sh
# dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLi
braries 
Building project ...
Finding the generator 'razorpage'...
Running the generator 'razorpage'...
Attempting to compile the application in memory.
Attempting to figure out the EntityFramework metadata for the model and DbContext: 'Movie'
Added Razor Page : /Pages/Movies/Create.cshtml
Added PageModel : /Pages/Movies/Create.cshtml.cs
Added Razor Page : /Pages/Movies/Edit.cshtml
Added PageModel : /Pages/Movies/Edit.cshtml.cs
Added Razor Page : /Pages/Movies/Details.cshtml
Added PageModel : /Pages/Movies/Details.cshtml.cs
Added Razor Page : /Pages/Movies/Delete.cshtml
Added PageModel : /Pages/Movies/Delete.cshtml.cs
Added Razor Page : /Pages/Movies/Index.cshtml
Added PageModel : /Pages/Movies/Index.cshtml.cs
RunTime 00:00:12.96
```

出力されたらしい。

{% capture text %}

## 最初の移行

`dotnet ef` がインストールされていない場合は、グローバルツールとしてインストールします。

```sh
dotnet tool install --global dotnet-ef
```

EF Core 用の CLI の詳細については、
[.Net CLI の EF Core ツール リファレンス](https://docs.microsoft.com/ja-jp/ef/core/miscellaneous/cli/dotnet)
を参照してください。

次の .NET Core CLI コマンドを実行します。

```sh
dotnet ef migrations add InitialCreate
dotnet ef database update
```

上記のコマンドで次の警告が生成されます。
"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。
これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。
'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"

この警告は無視して構いません。後のチュートリアルで修正されます。

移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。 
このスキーマは、 `DbContext` で指定されたモデルに基づきます。 `InitialCreate` 引数は移行の命名に使用されます。
任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。

`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。
この場合、`update` により、 _Migrations/<time-stamp>_InitialCreate.cs_ ファイルで `Up` メソッドが実行され、
データベースが作成されます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`dotnet-ef` はインストールしたはずなのでやってみる。

```sh
# dotnet ef migrations add InitialCreate
Build started...
Build succeeded.
Done. To undo this action, use 'ef migrations remove'
# dotnet ef database update
Build started...
Build succeeded.
```

特に問題なく通った。実行してみる。

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

問題なく動作したらしい。アクセスしてみる。

{% capture url %}{{site.github.url}}{% link _docs/AspDotNetCore/images/add-model/01_error.png %}{% endcapture %}
{% capture caption %}
エラーで表示されない。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

エラーが出て表示されない。
長くなったのでこの修正は次回にする。