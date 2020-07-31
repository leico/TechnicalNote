---
layout : post
title  : "Model を追加するためのセットアップ"
date   : 2020-07-27
lastchange : 2020-07-30 20:30:59
tags   :
  - docker
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## Model を追加する

[Razor Page プロジェクトを作成する]({{site.github.url}}{% link _docs/AspDotNetCore/create-project.md %}) 
では今回利用するプロジェクトディレクトリの作成と、自動的に生成されたファイルの概要を確認した。

今回は作ってたら長くなったのでモデルを作成するためのセットアップを行う。

参考:

* [パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
* [パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/first-mvc-app/adding-controller?view=aspnetcore-3.1&tabs=visual-studio "パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs")
* [NuGet とは何か。またどのような働きをするのか \| Microsoft Docs](https://docs.microsoft.com/ja-jp/nuget/what-is-nuget "NuGet とは何か。またどのような働きをするのか \| Microsoft Docs")
* [Entity Framework Core の概要 - EF Core \| Microsoft Docs](https://docs.microsoft.com/ja-jp/ef/core/ "Entity Framework Core の概要 - EF Core \| Microsoft Docs")
* [Entity Framework ドキュメント \| Microsoft Docs](https://docs.microsoft.com/ja-jp/ef/ "Entity Framework ドキュメント \| Microsoft Docs")
 



## モデルとその周辺について

{% capture text %}

モデル
: アプリのデータを表すクラス。モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。
  通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。
  このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、
  それをビューに提供するか、更新します。 更新されたデータはデータベースに書き込まれます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/first-mvc-app/adding-controller?view=aspnetcore-3.1&tabs=visual-studio "パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

つまり、データベースとやりとりをする機能を持ったクラスということになる。
他に、

{% capture text %}

ビュー
: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。
  一般に、この UI ではモデル データが表示されます。

コントローラー
: ブラウザー要求を処理するクラスです。 モデル データを取得し、応答を返すビュー テンプレートを呼び出します。 
  MVC アプリでは、ビューに情報のみが表示され、コントローラーがユーザーの入力と操作を処理して応答します。 
  たとえば、コントローラーはルート データとクエリ文字列の値を処理し、これらの値をモデルに渡します。 
  モデルはこれらの値を使用して、データベースを照会する場合があります。 
  たとえば、`https://localhost:5001/Home/Privacy` には、`Home` (コントローラー) と
  `Privacy` (ホーム コントローラーに対して呼び出すアクション メソッド) のルート データがあります。
  `https://localhost:5001/Movies/Edit/5` は、ムービー コントローラーを使用して `ID=5` のムービーを編集する要求です。
  ルート データについては、このチュートリアルで後ほど説明します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/first-mvc-app/adding-controller?view=aspnetcore-3.1&tabs=visual-studio "パート 2: ASP.NET Core MVC アプリにコントローラーを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

つまり、こういうことらしい。

{% capture url %}{{site.github.url}}{% link _docs/AspDotNetCore/images/add-model-setup/01_MVC.svg %}{% endcapture %}
{% capture caption %}
モデル、ビュー、コントローラの関係

[ソース](http://www.plantuml.com/plantuml/svg/RLBDJi906BplK-mBx0kGXl0WN2fK9C6IbFLaF7G_O-50g5631ZJ4AFuOOWp-Y19uc76BleNB7YoOthxEVFFDJBE5XdG3kRDTOpLlKt9Pvq6riYLvkHfu9Lcj-ymjoNh0GKFG5EgLiINX-fMQnw4kGLtGFTIJPTz0Dw0nA0OHr2FKhVriyLt6-OgGZa3NHhAEgh7TRe96XWFT_HCPjmZqOD2oAzqDjx448-iy1vtPOSBs6FjBonsHcl4Sr_F-OdIymWkHp_0rcxQLBQ0cXc8PIoAVPkAvD8e-wI3Srt4JoWozeW_wr5UjfFVyDJl_6KopRZb2P1zZAUg8V4RVDUV3ASAnZZuGTej-qdj7sFwU6MpEqh_WYEp6EXekueF5IvnSz7MddPmEMC7po-Pj_080)
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

この辺りはまた後のチュートリアルで詳しく触れることになりそうだ。
まずは、このチュートリアルを進めるためのツール郡をホスト側にインストールする。

## NuGet パッケージと EF ツールの追加

{% capture text %}

次の .NET Core CLI コマンドを実行します。

```sh
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

上記のコマンドにより次が追加されます。


* [aspnet-codegenerator スキャフォールディング ツール。](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/tools/dotnet-aspnet-codegenerator?view=aspnetcore-3.1)
* .NET Core CLI の Entity Framework Core ツール。
* EF Core SQLite プロバイダー。これにより、EF Core パッケージが依存関係としてインストールされます。
* スキャフォールディングに必要なパッケージ: `Microsoft.VisualStudio.Web.CodeGeneration.Design` と `Microsoft.EntityFrameworkCore.SqlServer` 。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




NuGet パッケージと EF ツールを追加する必要があるらしい。

{% capture text %}

**NuGet** では、.NET 用のパッケージを作成、ホスト、使用する方法が定義されており、それらの各ロール用のツールが提供されています。

つまり、NuGet パッケージは、拡張子が `.nupkg` の 1 つの ZIP ファイルであり、
コンパイル済みのコード (DLL)、そのコードに関連する他のファイル、
パッケージのバージョン番号などの情報が記述されているマニフェストが含まれます。 
開発者はコードを共有して、パッケージを作成し、それをパブリック ホスト
またはプライベート ホストに公開します。 パッケージ利用者は、
これらのパッケージを適切なホストから取得して、プロジェクトに追加した後、
プロジェクトのコードでパッケージの機能を呼び出します。 
その過程での細部はすべて NuGet 自体が処理します。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[NuGet とは何か。またどのような働きをするのか \| Microsoft Docs](https://docs.microsoft.com/ja-jp/nuget/what-is-nuget "NuGet とは何か。またどのような働きをするのか \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、
拡張性に優れ、 [オープン ソース](https://github.com/dotnet/efcore) で、プラットフォームに依存しません。

EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。
これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。
通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Entity Framework Core の概要 - EF Core \| Microsoft Docs](https://docs.microsoft.com/ja-jp/ef/core/ "Entity Framework Core の概要 - EF Core \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}

Entity Framework Core は、.NET 用の最新のオブジェクト データベース マッパーです。 
LINQ クエリ、変更の追跡、更新、スキーマの移行がサポートされています。 
EF Core は、SQL Database (オンプレミスと Azure)、SQLite、MySQL、PostgreSQL、
Azure Cosmos DB などの多くのデータベースに対応しています。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Entity Framework ドキュメント \| Microsoft Docs](https://docs.microsoft.com/ja-jp/ef/ "Entity Framework ドキュメント \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

つまり、簡単にデータベースを操作できるためのプラグインを入れるということらしい。

コンテナ側でやってみる。まずは `dotnet-ef` を入れる。

```sh
# dotnet tool install --global dotnet-ef
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
```

パスの設定が必要らしい。設定する。

```sh
# vi /root/.bash_profile
```

```sh
export PATH="$PATH:/root/.dotnet/tools"
```

```sh
# source /root/.bash_profile
# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.dotnet/tools
```

設定した。
ただ、ここまでの設定はコンテナを終了してしまうと消えてしまう。
どこかで _Dockerfile_ を編集する必要がありそうだ。

次は `dotnet-aspnet-codegenerator` を入れる。

```sh
# dotnet tool install --global dotnet-aspnet-codegenerator
You can invoke the tool using the following command: dotnet-aspnet-codegenerator
Tool 'dotnet-aspnet-codegenerator' (version '3.1.3') was successfully installed.
```

入った。 Package も入れてみる。 まずは `SQLite`

```sh
# dotnet add package Microsoft.EntityFrameworkCore.SQLite
  Determining projects to restore...
  Writing /tmp/tmpFFKFFj.tmp
info : Adding PackageReference for package 'Microsoft.EntityFrameworkCore.SQLite' into project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Restoring packages for /Projects/RazorPagesMovie/RazorPagesMovie.csproj...
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite/index.json 900ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite/3.1.6/microsoft.entityframeworkcore.sqlite.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite/3.1.6/microsoft.entityframeworkcore.sqlite.3.1.6.nupkg 26ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite.core/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.bundle_e_sqlite3/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite.core/index.json 591ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite.core/3.1.6/microsoft.entityframeworkcore.sqlite.core.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlite.core/3.1.6/microsoft.entityframeworkcore.sqlite.core.3.1.6.nupkg 26ms
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.bundle_e_sqlite3/index.json 638ms
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.bundle_e_sqlite3/2.0.2/sqlitepclraw.bundle_e_sqlite3.2.0.2.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.relational/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlite.core/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.dotnet.platformabstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencymodel/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.relational/index.json 157ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.relational/3.1.6/microsoft.entityframeworkcore.relational.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.relational/3.1.6/microsoft.entityframeworkcore.relational.3.1.6.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencymodel/index.json 214ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencymodel/3.1.6/microsoft.extensions.dependencymodel.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencymodel/3.1.6/microsoft.extensions.dependencymodel.3.1.6.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.json/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.bundle_e_sqlite3/2.0.2/sqlitepclraw.bundle_e_sqlite3.2.0.2.nupkg 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.core/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.provider.dynamic_cdecl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.lib.e_sqlite3/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.dotnet.platformabstractions/index.json 641ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlite.core/index.json 647ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.dotnet.platformabstractions/3.1.6/microsoft.dotnet.platformabstractions.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlite.core/3.1.6/microsoft.data.sqlite.core.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.dotnet.platformabstractions/3.1.6/microsoft.dotnet.platformabstractions.3.1.6.nupkg 14ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlite.core/3.1.6/microsoft.data.sqlite.core.3.1.6.nupkg 16ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore/3.1.6/microsoft.entityframeworkcore.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore/3.1.6/microsoft.entityframeworkcore.3.1.6.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.abstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.analyzers/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.bcl.asyncinterfaces/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.bcl.hashcode/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.memory/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.immutable/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.json/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.annotations/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.json/4.7.2/system.text.json.4.7.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.json/4.7.2/system.text.json.4.7.2.nupkg 58ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.bcl.asyncinterfaces/index.json 156ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.bcl.asyncinterfaces/1.1.1/microsoft.bcl.asyncinterfaces.1.1.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.bcl.asyncinterfaces/1.1.1/microsoft.bcl.asyncinterfaces.1.1.1.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.core/index.json 592ms
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.core/2.0.2/sqlitepclraw.core.2.0.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.provider.dynamic_cdecl/index.json 633ms
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.provider.dynamic_cdecl/2.0.2/sqlitepclraw.provider.dynamic_cdecl.2.0.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.lib.e_sqlite3/index.json 644ms
info :   GET https://api.nuget.org/v3-flatcontainer/sqlitepclraw.lib.e_sqlite3/2.0.2/sqlitepclraw.lib.e_sqlite3.2.0.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.abstractions/index.json 593ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.abstractions/3.1.6/microsoft.entityframeworkcore.abstractions.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.analyzers/index.json 596ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.annotations/index.json 597ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.analyzers/3.1.6/microsoft.entityframeworkcore.analyzers.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.annotations/4.7.0/system.componentmodel.annotations.4.7.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.abstractions/3.1.6/microsoft.entityframeworkcore.abstractions.3.1.6.nupkg 21ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.analyzers/3.1.6/microsoft.entityframeworkcore.analyzers.3.1.6.nupkg 16ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.annotations/4.7.0/system.componentmodel.annotations.4.7.0.nupkg 17ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.bcl.hashcode/index.json 635ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/index.json 634ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.memory/index.json 635ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.immutable/index.json 633ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/3.1.6/microsoft.extensions.dependencyinjection.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.memory/3.1.6/microsoft.extensions.caching.memory.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.immutable/1.7.1/system.collections.immutable.1.7.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/index.json 634ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.bcl.hashcode/1.1.0/microsoft.bcl.hashcode.1.1.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/4.7.1/system.diagnostics.diagnosticsource.4.7.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging/index.json 645ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging/3.1.6/microsoft.extensions.logging.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/3.1.6/microsoft.extensions.dependencyinjection.3.1.6.nupkg 19ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.memory/3.1.6/microsoft.extensions.caching.memory.3.1.6.nupkg 19ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.bcl.hashcode/1.1.0/microsoft.bcl.hashcode.1.1.0.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/4.7.1/system.diagnostics.diagnosticsource.4.7.1.nupkg 25ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.immutable/1.7.1/system.collections.immutable.1.7.1.nupkg 30ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging/3.1.6/microsoft.extensions.logging.3.1.6.nupkg 22ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.abstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging.abstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.options/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.binder/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/index.json 173ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/3.1.6/microsoft.extensions.dependencyinjection.abstractions.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/3.1.6/microsoft.extensions.dependencyinjection.abstractions.3.1.6.nupkg 33ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.binder/index.json 172ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.binder/3.1.6/microsoft.extensions.configuration.binder.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.core/2.0.2/sqlitepclraw.core.2.0.2.nupkg 597ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.memory/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.binder/3.1.6/microsoft.extensions.configuration.binder.3.1.6.nupkg 20ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.provider.dynamic_cdecl/2.0.2/sqlitepclraw.provider.dynamic_cdecl.2.0.2.nupkg 586ms
info :   OK https://api.nuget.org/v3-flatcontainer/sqlitepclraw.lib.e_sqlite3/2.0.2/sqlitepclraw.lib.e_sqlite3.2.0.2.nupkg 584ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration/index.json 225ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration/3.1.6/microsoft.extensions.configuration.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration/3.1.6/microsoft.extensions.configuration.3.1.6.nupkg 38ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.abstractions/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.abstractions/index.json 618ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.options/index.json 616ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging.abstractions/index.json 618ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.abstractions/3.1.6/microsoft.extensions.caching.abstractions.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.options/3.1.6/microsoft.extensions.options.3.1.6.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging.abstractions/3.1.6/microsoft.extensions.logging.abstractions.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.caching.abstractions/3.1.6/microsoft.extensions.caching.abstractions.3.1.6.nupkg 28ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.options/3.1.6/microsoft.extensions.options.3.1.6.nupkg 36ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.primitives/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.logging.abstractions/3.1.6/microsoft.extensions.logging.abstractions.3.1.6.nupkg 83ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.memory/index.json 612ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.memory/4.5.3/system.memory.4.5.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.memory/4.5.3/system.memory.4.5.3.nupkg 30ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.abstractions/index.json 590ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.abstractions/3.1.6/microsoft.extensions.configuration.abstractions.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.configuration.abstractions/3.1.6/microsoft.extensions.configuration.abstractions.3.1.6.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.primitives/index.json 594ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.primitives/3.1.6/microsoft.extensions.primitives.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.primitives/3.1.6/microsoft.extensions.primitives.3.1.6.nupkg 22ms
info : Installing Microsoft.Extensions.Configuration.Abstractions 3.1.6.
info : Installing Microsoft.Extensions.Configuration 3.1.6.
info : Installing Microsoft.Extensions.Primitives 3.1.6.
info : Installing Microsoft.EntityFrameworkCore.Sqlite 3.1.6.
info : Installing Microsoft.Extensions.Configuration.Binder 3.1.6.
info : Installing Microsoft.Extensions.Logging.Abstractions 3.1.6.
info : Installing Microsoft.EntityFrameworkCore.Sqlite.Core 3.1.6.
info : Installing Microsoft.Extensions.Options 3.1.6.
info : Installing Microsoft.Extensions.Caching.Abstractions 3.1.6.
info : Installing SQLitePCLRaw.bundle_e_sqlite3 2.0.2.
info : Installing Microsoft.DotNet.PlatformAbstractions 3.1.6.
info : Installing Microsoft.Extensions.DependencyInjection.Abstractions 3.1.6.
info : Installing Microsoft.Extensions.Logging 3.1.6.
info : Installing Microsoft.Extensions.DependencyModel 3.1.6.
info : Installing Microsoft.Extensions.Caching.Memory 3.1.6.
info : Installing Microsoft.Data.Sqlite.Core 3.1.6.
info : Installing Microsoft.EntityFrameworkCore.Relational 3.1.6.
info : Installing System.Collections.Immutable 1.7.1.
info : Installing Microsoft.Extensions.DependencyInjection 3.1.6.
info : Installing System.Diagnostics.DiagnosticSource 4.7.1.
info : Installing Microsoft.Bcl.HashCode 1.1.0.
info : Installing SQLitePCLRaw.provider.dynamic_cdecl 2.0.2.
info : Installing System.ComponentModel.Annotations 4.7.0.
info : Installing Microsoft.EntityFrameworkCore.Analyzers 3.1.6.
info : Installing SQLitePCLRaw.core 2.0.2.
info : Installing Microsoft.EntityFrameworkCore.Abstractions 3.1.6.
info : Installing SQLitePCLRaw.lib.e_sqlite3 2.0.2.
info : Installing System.Text.Json 4.7.2.
info : Installing Microsoft.EntityFrameworkCore 3.1.6.
info : Installing Microsoft.Bcl.AsyncInterfaces 1.1.1.
info : Installing System.Memory 4.5.3.
info : Package 'Microsoft.EntityFrameworkCore.SQLite' is compatible with all the specified frameworks in project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : PackageReference for package 'Microsoft.EntityFrameworkCore.SQLite' version '3.1.6' added to file '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Committing restore...
info : Writing assets file to disk. Path: /Projects/RazorPagesMovie/obj/project.assets.json
log  : Restored /Projects/RazorPagesMovie/RazorPagesMovie.csproj (in 6.16 sec).
```

結構ログが長いが入ったらしい。 次 `VisulaStudio.Web.CodeGeneration.Design`

```sh
# dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  Determining projects to restore...
  Writing /tmp/tmpnEdNgB.tmp
info : Adding PackageReference for package 'Microsoft.VisualStudio.Web.CodeGeneration.Design' into project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Restoring packages for /Projects/RazorPagesMovie/RazorPagesMovie.csproj...
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.design/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.design/index.json 772ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.design/3.1.3/microsoft.visualstudio.web.codegeneration.design.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.design/3.1.3/microsoft.visualstudio.web.codegeneration.design.3.1.3.nupkg 36ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegenerators.mvc/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegenerators.mvc/index.json 706ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegenerators.mvc/3.1.3/microsoft.visualstudio.web.codegenerators.mvc.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegenerators.mvc/3.1.3/microsoft.visualstudio.web.codegenerators.mvc.3.1.3.nupkg 22ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration/index.json 623ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration/3.1.3/microsoft.visualstudio.web.codegeneration.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration/3.1.3/microsoft.visualstudio.web.codegeneration.3.1.3.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.entityframeworkcore/index.json
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/3.1.0/microsoft.extensions.dependencyinjection.3.1.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection/3.1.0/microsoft.extensions.dependencyinjection.3.1.0.nupkg 46ms
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/3.1.0/microsoft.extensions.dependencyinjection.abstractions.3.1.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.extensions.dependencyinjection.abstractions/3.1.0/microsoft.extensions.dependencyinjection.abstractions.3.1.0.nupkg 13ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.entityframeworkcore/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.entityframeworkcore/3.1.3/microsoft.visualstudio.web.codegeneration.entityframeworkcore.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.entityframeworkcore/3.1.3/microsoft.visualstudio.web.codegeneration.entityframeworkcore.3.1.3.nupkg 12ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.core/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.core/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.core/3.1.3/microsoft.visualstudio.web.codegeneration.core.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.core/3.1.3/microsoft.visualstudio.web.codegeneration.core.3.1.3.nupkg 8ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.templating/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/newtonsoft.json/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.templating/index.json 586ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.templating/3.1.3/microsoft.visualstudio.web.codegeneration.templating.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/newtonsoft.json/index.json 598ms
info :   GET https://api.nuget.org/v3-flatcontainer/newtonsoft.json/11.0.2/newtonsoft.json.11.0.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.templating/3.1.3/microsoft.visualstudio.web.codegeneration.templating.3.1.3.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.utils/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.language/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/newtonsoft.json/11.0.2/newtonsoft.json.11.0.2.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.runtime/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.razor/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.utils/index.json 586ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.utils/3.1.3/microsoft.visualstudio.web.codegeneration.utils.3.1.3.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.utils/3.1.3/microsoft.visualstudio.web.codegeneration.utils.3.1.3.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.contracts/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp.workspaces/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/nuget.frameworks/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.razor/index.json 627ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp/index.json 628ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.razor/3.1.0/microsoft.codeanalysis.razor.3.1.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp/3.3.1/microsoft.codeanalysis.csharp.3.3.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.runtime/index.json 634ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.runtime/2.2.0/microsoft.aspnetcore.razor.runtime.2.2.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.language/index.json 641ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.language/3.1.0/microsoft.aspnetcore.razor.language.3.1.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.razor/3.1.0/microsoft.codeanalysis.razor.3.1.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp/3.3.1/microsoft.codeanalysis.csharp.3.3.1.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.runtime/2.2.0/microsoft.aspnetcore.razor.runtime.2.2.0.nupkg 17ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor.language/3.1.0/microsoft.aspnetcore.razor.language.3.1.0.nupkg 16ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.html.abstractions/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.contracts/index.json 583ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp.workspaces/index.json 582ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.contracts/3.1.3/microsoft.visualstudio.web.codegeneration.contracts.3.1.3.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp.workspaces/3.3.1/microsoft.codeanalysis.csharp.workspaces.3.3.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.csharp.workspaces/3.3.1/microsoft.codeanalysis.csharp.workspaces.3.3.1.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.visualstudio.web.codegeneration.contracts/3.1.3/microsoft.visualstudio.web.codegeneration.contracts.3.1.3.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/nuget.frameworks/index.json 622ms
info :   GET https://api.nuget.org/v3-flatcontainer/nuget.frameworks/4.7.0/nuget.frameworks.4.7.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.workspaces.common/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/index.json 575ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/3.3.0/microsoft.codeanalysis.common.3.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/3.3.1/microsoft.codeanalysis.common.3.3.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/nuget.frameworks/4.7.0/nuget.frameworks.4.7.0.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/netstandard.library/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/3.3.1/microsoft.codeanalysis.common.3.3.1.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.common/3.3.0/microsoft.codeanalysis.common.3.3.0.nupkg 13ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor/index.json 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor/2.2.0/microsoft.aspnetcore.razor.2.2.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.razor/2.2.0/microsoft.aspnetcore.razor.2.2.0.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.html.abstractions/index.json 646ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.html.abstractions/2.2.0/microsoft.aspnetcore.html.abstractions.2.2.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/index.json
info :   CACHE https://api.nuget.org/v3-flatcontainer/system.collections.immutable/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.aspnetcore.html.abstractions/2.2.0/microsoft.aspnetcore.html.abstractions.2.2.0.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.immutable/1.5.0/system.collections.immutable.1.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.metadata/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encodings.web/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.immutable/1.5.0/system.collections.immutable.1.5.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.workspaces.common/index.json 155ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.workspaces.common/3.3.1/microsoft.codeanalysis.workspaces.common.3.3.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.workspaces.common/3.3.1/microsoft.codeanalysis.workspaces.common.3.3.1.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/netstandard.library/index.json 576ms
info :   GET https://api.nuget.org/v3-flatcontainer/netstandard.library/1.6.1/netstandard.library.1.6.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/netstandard.library/1.6.1/netstandard.library.1.6.1.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.win32.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.appcontext/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.concurrent/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.console/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.debug/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.tools/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.tracing/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization.calendars/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.io/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.compression/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.compression.zipfile/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.filesystem/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.filesystem.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.linq/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.linq.expressions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.http/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.sockets/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.objectmodel/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.extensions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.resources.resourcemanager/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.extensions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.handles/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices.runtimeinformation/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.numerics/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.algorithms/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.encoding/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.x509certificates/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding.extensions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.regularexpressions/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.tasks/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.timer/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.readerwriter/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xdocument/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/index.json 578ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.metadata/index.json 577ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/index.json 581ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/index.json 592ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.metadata/1.6.0/system.reflection.metadata.1.6.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/4.5.3/system.threading.tasks.extensions.4.5.3.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/4.5.2/system.runtime.compilerservices.unsafe.4.5.2.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/2.9.3/microsoft.codeanalysis.analyzers.2.9.3.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/2.9.4/microsoft.codeanalysis.analyzers.2.9.4.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/4.5.3/system.threading.tasks.extensions.4.5.3.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.metadata/1.6.0/system.reflection.metadata.1.6.0.nupkg 14ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/4.5.2/system.runtime.compilerservices.unsafe.4.5.2.nupkg 18ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/2.9.3/microsoft.codeanalysis.analyzers.2.9.3.nupkg 17ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/index.json 648ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/4.5.1/system.text.encoding.codepages.4.5.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encodings.web/index.json 654ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encodings.web/4.5.0/system.text.encodings.web.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/4.5.1/system.text.encoding.codepages.4.5.1.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encodings.web/4.5.0/system.text.encodings.web.4.5.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.codeanalysis.analyzers/2.9.4/microsoft.codeanalysis.analyzers.2.9.4.nupkg 72ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.compression/index.json 229ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.compression/4.3.0/system.io.compression.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.extensions/index.json 219ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.extensions/4.3.0/system.runtime.extensions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.extensions/index.json 228ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.extensions/4.3.0/system.reflection.extensions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.compression/4.3.0/system.io.compression.4.3.0.nupkg 14ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.extensions/4.3.0/system.runtime.extensions.4.3.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.extensions/4.3.0/system.reflection.extensions.4.3.0.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding.extensions/index.json 222ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding.extensions/4.3.0/system.text.encoding.extensions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xdocument/index.json 217ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xdocument/4.3.0/system.xml.xdocument.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xdocument/4.3.0/system.xml.xdocument.4.3.0.nupkg 23ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.targets/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding.extensions/4.3.0/system.text.encoding.extensions.4.3.0.nupkg 44ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.io.compression/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.buffers/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition/index.json 586ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition/1.0.31/system.composition.1.0.31.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition/1.0.31/system.composition.1.0.31.nupkg 21ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.attributedmodel/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.convention/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.hosting/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.runtime/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.typedparts/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.buffers/index.json 155ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.buffers/4.3.0/system.buffers.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.buffers/4.3.0/system.buffers.4.3.0.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.typedparts/index.json 154ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.typedparts/1.0.31/system.composition.typedparts.1.0.31.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.typedparts/1.0.31/system.composition.typedparts.1.0.31.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.appcontext/index.json 576ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/index.json 577ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.appcontext/4.3.0/system.appcontext.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/1.1.0/microsoft.netcore.platforms.1.1.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/2.1.2/microsoft.netcore.platforms.2.1.2.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.win32.primitives/index.json 581ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.win32.primitives/4.3.0/microsoft.win32.primitives.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.appcontext/4.3.0/system.appcontext.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/1.1.0/microsoft.netcore.platforms.1.1.0.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/2.1.2/microsoft.netcore.platforms.2.1.2.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.win32.primitives/4.3.0/microsoft.win32.primitives.4.3.0.nupkg 16ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.concurrent/index.json 631ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.concurrent/4.3.0/system.collections.concurrent.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.io/index.json 631ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.io/4.3.0/system.io.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization/index.json 635ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.tracing/index.json 636ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.tools/index.json 637ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization/4.3.0/system.globalization.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.tools/4.3.0/system.diagnostics.tools.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.tracing/4.3.0/system.diagnostics.tracing.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.console/index.json 641ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization.calendars/index.json 638ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.console/4.3.0/system.console.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization.calendars/4.3.0/system.globalization.calendars.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.debug/index.json 643ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.concurrent/4.3.0/system.collections.concurrent.4.3.0.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.debug/4.3.0/system.diagnostics.debug.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.io/4.3.0/system.io.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.linq.expressions/index.json 639ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.tools/4.3.0/system.diagnostics.tools.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization/4.3.0/system.globalization.4.3.0.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.linq.expressions/4.3.0/system.linq.expressions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.console/4.3.0/system.console.4.3.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.tracing/4.3.0/system.diagnostics.tracing.4.3.0.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization.calendars/4.3.0/system.globalization.calendars.4.3.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.linq/index.json 648ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.debug/4.3.0/system.diagnostics.debug.4.3.0.nupkg 13ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.linq/4.3.0/system.linq.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime/index.json 640ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.encoding/index.json 636ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime/4.3.0/system.runtime.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.primitives/index.json 648ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.encoding/4.3.0/system.security.cryptography.encoding.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.filesystem.primitives/index.json 658ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.primitives/4.3.0/system.reflection.primitives.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.filesystem.primitives/4.3.0/system.io.filesystem.primitives.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.linq.expressions/4.3.0/system.linq.expressions.4.3.0.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.numerics/index.json 651ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices.runtimeinformation/index.json 652ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.compression.zipfile/index.json 670ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.numerics/4.3.0/system.runtime.numerics.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.compression.zipfile/4.3.0/system.io.compression.zipfile.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices.runtimeinformation/4.3.0/system.runtime.interopservices.runtimeinformation.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.linq/4.3.0/system.linq.4.3.0.nupkg 19ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.objectmodel/index.json 665ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime/4.3.0/system.runtime.4.3.0.nupkg 15ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices/index.json 656ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.resources.resourcemanager/index.json 663ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices/4.3.0/system.runtime.interopservices.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.objectmodel/4.3.0/system.objectmodel.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.filesystem/index.json 675ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.primitives/index.json 654ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.resources.resourcemanager/4.3.0/system.resources.resourcemanager.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.primitives/4.3.0/system.security.cryptography.primitives.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.io.filesystem/4.3.0/system.io.filesystem.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.algorithms/index.json 660ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.encoding/4.3.0/system.security.cryptography.encoding.4.3.0.nupkg 22ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.x509certificates/index.json 657ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.algorithms/4.3.0/system.security.cryptography.algorithms.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.primitives/4.3.0/system.reflection.primitives.4.3.0.nupkg 20ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.tasks/index.json 641ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.x509certificates/4.3.0/system.security.cryptography.x509certificates.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.tasks/4.3.0/system.threading.tasks.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding/index.json 652ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading/index.json 649ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.filesystem.primitives/4.3.0/system.io.filesystem.primitives.4.3.0.nupkg 24ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding/4.3.0/system.text.encoding.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading/4.3.0/system.threading.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection/index.json 679ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection/4.3.0/system.reflection.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.regularexpressions/index.json 655ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.primitives/index.json 686ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.regularexpressions/4.3.0/system.text.regularexpressions.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.primitives/4.3.0/system.net.primitives.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.numerics/4.3.0/system.runtime.numerics.4.3.0.nupkg 22ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.compression.zipfile/4.3.0/system.io.compression.zipfile.4.3.0.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.handles/index.json 680ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.readerwriter/index.json 654ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.handles/4.3.0/system.runtime.handles.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.readerwriter/4.3.0/system.xml.readerwriter.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.timer/index.json 664ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.timer/4.3.0/system.threading.timer.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.objectmodel/4.3.0/system.objectmodel.4.3.0.nupkg 35ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.resources.resourcemanager/4.3.0/system.resources.resourcemanager.4.3.0.nupkg 37ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections/index.json 722ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.io.filesystem/4.3.0/system.io.filesystem.4.3.0.nupkg 36ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.primitives/4.3.0/system.security.cryptography.primitives.4.3.0.nupkg 37ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices.runtimeinformation/4.3.0/system.runtime.interopservices.runtimeinformation.4.3.0.nupkg 42ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.algorithms/4.3.0/system.security.cryptography.algorithms.4.3.0.nupkg 33ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections/4.3.0/system.collections.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.tasks/4.3.0/system.threading.tasks.4.3.0.nupkg 34ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.regularexpressions/4.3.0/system.text.regularexpressions.4.3.0.nupkg 28ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.primitives/4.3.0/system.net.primitives.4.3.0.nupkg 28ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.handles/4.3.0/system.runtime.handles.4.3.0.nupkg 31ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.readerwriter/4.3.0/system.xml.readerwriter.4.3.0.nupkg 31ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.x509certificates/4.3.0/system.security.cryptography.x509certificates.4.3.0.nupkg 53ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding/4.3.0/system.text.encoding.4.3.0.nupkg 49ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading/4.3.0/system.threading.4.3.0.nupkg 49ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection/4.3.0/system.reflection.4.3.0.nupkg 48ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.timer/4.3.0/system.threading.timer.4.3.0.nupkg 47ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections/4.3.0/system.collections.4.3.0.nupkg 65ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.targets/index.json 586ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.targets/1.1.0/microsoft.netcore.targets.1.1.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.http/index.json 873ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.sockets/index.json 872ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.sockets/4.3.0/system.net.sockets.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.http/4.3.0/system.net.http.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.targets/1.1.0/microsoft.netcore.targets.1.1.0.nupkg 17ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system/index.json 587ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system/4.3.0/runtime.native.system.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.io.compression/index.json 587ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.io.compression/4.3.0/runtime.native.system.io.compression.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.http/4.3.0/system.net.http.4.3.0.nupkg 25ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.sockets/4.3.0/system.net.sockets.4.3.0.nupkg 26ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system/4.3.0/runtime.native.system.4.3.0.nupkg 19ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.io.compression/4.3.0/runtime.native.system.io.compression.4.3.0.nupkg 41ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.runtime/index.json 575ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.runtime/1.0.31/system.composition.runtime.1.0.31.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.attributedmodel/index.json 582ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.convention/index.json 581ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.convention/1.0.31/system.composition.convention.1.0.31.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.hosting/index.json 582ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.attributedmodel/1.0.31/system.composition.attributedmodel.1.0.31.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.composition.hosting/1.0.31/system.composition.hosting.1.0.31.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.runtime/1.0.31/system.composition.runtime.1.0.31.nupkg 13ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.attributedmodel/1.0.31/system.composition.attributedmodel.1.0.31.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.convention/1.0.31/system.composition.convention.1.0.31.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.composition.hosting/1.0.31/system.composition.hosting.1.0.31.nupkg 12ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.apple/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.interopservices/4.3.0/system.runtime.interopservices.4.3.0.nupkg 594ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.net.http/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.cng/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.csp/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/4.3.0/system.threading.tasks.extensions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.threading.tasks.extensions/4.3.0/system.threading.tasks.extensions.4.3.0.nupkg 9ms
info :   CACHE https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/4.3.0/system.diagnostics.diagnosticsource.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization.extensions/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.openssl/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.openssl/4.3.0/runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.diagnostics.diagnosticsource/4.3.0/system.diagnostics.diagnosticsource.4.3.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.openssl/4.3.0/runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.apple/index.json 578ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.apple/4.3.0/runtime.native.system.security.cryptography.apple.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.security.cryptography.apple/4.3.0/runtime.native.system.security.cryptography.apple.4.3.0.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.net.http/index.json 580ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.csp/index.json 579ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.csp/4.3.0/system.security.cryptography.csp.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.openssl/index.json 582ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.cng/index.json 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.net.http/4.3.0/runtime.native.system.net.http.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.openssl/4.3.0/system.security.cryptography.openssl.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.cng/4.3.0/system.security.cryptography.cng.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.csp/4.3.0/system.security.cryptography.csp.4.3.0.nupkg 9ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.net.http/4.3.0/runtime.native.system.net.http.4.3.0.nupkg 13ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit.ilgeneration/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit.lightweight/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.typeextensions/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.cng/4.3.0/system.security.cryptography.cng.4.3.0.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.openssl/4.3.0/system.security.cryptography.openssl.4.3.0.nupkg 13ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit.ilgeneration/index.json 155ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.typeextensions/index.json 159ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit.lightweight/index.json 160ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit.ilgeneration/4.3.0/system.reflection.emit.ilgeneration.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.typeextensions/4.3.0/system.reflection.typeextensions.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit.lightweight/4.3.0/system.reflection.emit.lightweight.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit.ilgeneration/4.3.0/system.reflection.emit.ilgeneration.4.3.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.typeextensions/4.3.0/system.reflection.typeextensions.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit.lightweight/4.3.0/system.reflection.emit.lightweight.4.3.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization.extensions/index.json 578ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.globalization.extensions/4.3.0/system.globalization.extensions.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.globalization.extensions/4.3.0/system.globalization.extensions.4.3.0.nupkg 8ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl/index.json 574ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl/index.json 574ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl/index.json 579ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl/index.json 582ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl/index.json 579ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl/index.json 583ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl/index.json 584ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl/index.json 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.fedora.24-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl/index.json 583ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.rhel.7-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 11ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.opensuse.13.2-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.fedora.23-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.debian.8-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 15ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.14.04-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 10ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.16.04-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 15ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.ubuntu.16.10-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 11ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.opensuse.42.1-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl/4.3.0/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.openssl.4.3.0.nupkg 14ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple/index.json 578ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple/4.3.0/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple/4.3.0/runtime.osx.10.10-x64.runtime.native.system.security.cryptography.apple.4.3.0.nupkg 12ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit/index.json 580ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.reflection.emit/4.3.0/system.reflection.emit.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.reflection.emit/4.3.0/system.reflection.emit.4.3.0.nupkg 8ms
info : Installing runtime.osx.10.10-x64.runtime.native.System.Security.Cryptography.Apple 4.3.0.
info : Installing runtime.ubuntu.16.04-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.osx.10.10-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.opensuse.42.1-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.opensuse.13.2-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.ubuntu.16.10-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.debian.8-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.fedora.23-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.ubuntu.14.04-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.rhel.7-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.fedora.24-x64.runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing System.Composition.TypedParts 1.0.31.
info : Installing System.Composition.Convention 1.0.31.
info : Installing System.Composition.Hosting 1.0.31.
info : Installing System.Composition.Runtime 1.0.31.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.Design 3.1.3.
info : Installing Microsoft.VisualStudio.Web.CodeGenerators.Mvc 3.1.3.
info : Installing System.Composition.AttributedModel 1.0.31.
info : Installing System.Reflection.Emit 4.3.0.
info : Installing System.Reflection.TypeExtensions 4.3.0.
info : Installing System.Reflection.Emit.ILGeneration 4.3.0.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration 3.1.3.
info : Installing Microsoft.Extensions.DependencyInjection 3.1.0.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.EntityFrameworkCore 3.1.3.
info : Installing Microsoft.Extensions.DependencyInjection.Abstractions 3.1.0.
info : Installing System.Reflection.Emit.Lightweight 4.3.0.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.Core 3.1.3.
info : Installing Newtonsoft.Json 11.0.2.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.Templating 3.1.3.
info : Installing Microsoft.AspNetCore.Razor.Language 3.1.0.
info : Installing runtime.native.System.Security.Cryptography.Apple 4.3.0.
info : Installing System.Globalization.Extensions 4.3.0.
info : Installing Microsoft.AspNetCore.Razor.Runtime 2.2.0.
info : Installing Microsoft.CodeAnalysis.CSharp 3.3.1.
info : Installing System.Diagnostics.DiagnosticSource 4.3.0.
info : Installing System.Security.Cryptography.Csp 4.3.0.
info : Installing System.Security.Cryptography.Cng 4.3.0.
info : Installing System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing runtime.native.System.Net.Http 4.3.0.
info : Installing runtime.native.System.Security.Cryptography.OpenSsl 4.3.0.
info : Installing System.Threading.Tasks.Extensions 4.3.0.
info : Installing Microsoft.CodeAnalysis.Razor 3.1.0.
info : Installing runtime.native.System.IO.Compression 4.3.0.
info : Installing runtime.native.System 4.3.0.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.Utils 3.1.3.
info : Installing Microsoft.NETCore.Targets 1.1.0.
info : Installing Microsoft.AspNetCore.Razor 2.2.0.
info : Installing System.Buffers 4.3.0.
info : Installing Microsoft.AspNetCore.Html.Abstractions 2.2.0.
info : Installing System.Composition 1.0.31.
info : Installing Microsoft.CodeAnalysis.Common 3.3.1.
info : Installing System.Linq.Expressions 4.3.0.
info : Installing Microsoft.CodeAnalysis.Common 3.3.0.
info : Installing System.Runtime 4.3.0.
info : Installing Microsoft.VisualStudio.Web.CodeGeneration.Contracts 3.1.3.
info : Installing NuGet.Frameworks 4.7.0.
info : Installing Microsoft.CodeAnalysis.CSharp.Workspaces 3.3.1.
info : Installing System.Text.Encodings.Web 4.5.0.
info : Installing System.Collections.Immutable 1.5.0.
info : Installing System.Threading.Tasks.Extensions 4.5.3.
info : Installing System.Runtime.CompilerServices.Unsafe 4.5.2.
info : Installing System.Reflection.Metadata 1.6.0.
info : Installing Microsoft.CodeAnalysis.Analyzers 2.9.4.
info : Installing System.Text.Encoding.CodePages 4.5.1.
info : Installing Microsoft.CodeAnalysis.Analyzers 2.9.3.
info : Installing NETStandard.Library 1.6.1.
info : Installing Microsoft.CodeAnalysis.Workspaces.Common 3.3.1.
info : Installing System.Security.Cryptography.Algorithms 4.3.0.
info : Installing Microsoft.NETCore.Platforms 2.1.2.
info : Installing Microsoft.NETCore.Platforms 1.1.0.
info : Installing System.AppContext 4.3.0.
info : Installing System.Net.Http 4.3.0.
info : Installing System.Xml.XDocument 4.3.0.
info : Installing System.IO.FileSystem.Primitives 4.3.0.
info : Installing System.IO.Compression.ZipFile 4.3.0.
info : Installing System.Runtime.Numerics 4.3.0.
info : Installing System.Collections.Concurrent 4.3.0.
info : Installing System.ObjectModel 4.3.0.
info : Installing System.Security.Cryptography.Primitives 4.3.0.
info : Installing System.Reflection.Extensions 4.3.0.
info : Installing System.IO.FileSystem 4.3.0.
info : Installing System.Reflection.Primitives 4.3.0.
info : Installing System.Text.Encoding 4.3.0.
info : Installing System.Runtime.InteropServices.RuntimeInformation 4.3.0.
info : Installing System.Runtime.Extensions 4.3.0.
info : Installing System.Text.Encoding.Extensions 4.3.0.
info : Installing Microsoft.Win32.Primitives 4.3.0.
info : Installing System.Diagnostics.Tools 4.3.0.
info : Installing System.Globalization.Calendars 4.3.0.
info : Installing System.Diagnostics.Debug 4.3.0.
info : Installing System.Runtime.Handles 4.3.0.
info : Installing System.Resources.ResourceManager 4.3.0.
info : Installing System.Globalization 4.3.0.
info : Installing System.Threading.Timer 4.3.0.
info : Installing System.Console 4.3.0.
info : Installing System.IO 4.3.0.
info : Installing System.Diagnostics.Tracing 4.3.0.
info : Installing System.Net.Sockets 4.3.0.
info : Installing System.IO.Compression 4.3.0.
info : Installing System.Threading.Tasks 4.3.0.
info : Installing System.Net.Primitives 4.3.0.
info : Installing System.Linq 4.3.0.
info : Installing System.Reflection 4.3.0.
info : Installing System.Text.RegularExpressions 4.3.0.
info : Installing System.Runtime.InteropServices 4.3.0.
info : Installing System.Collections 4.3.0.
info : Installing System.Xml.ReaderWriter 4.3.0.
info : Installing System.Threading 4.3.0.
info : Installing System.Security.Cryptography.Encoding 4.3.0.
info : Installing System.Security.Cryptography.X509Certificates 4.3.0.
info : Package 'Microsoft.VisualStudio.Web.CodeGeneration.Design' is compatible with all the specified frameworks in project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : PackageReference for package 'Microsoft.VisualStudio.Web.CodeGeneration.Design' version '3.1.3' added to file '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Committing restore...
info : Generating MSBuild file /Projects/RazorPagesMovie/obj/RazorPagesMovie.csproj.nuget.g.props.
info : Writing assets file to disk. Path: /Projects/RazorPagesMovie/obj/project.assets.json
log  : Restored /Projects/RazorPagesMovie/RazorPagesMovie.csproj (in 9.62 sec).
```

終わった。次 `MicrosoftEntityFrameworkCore.Design`

```sh
 # dotnet add package Microsoft.EntityFrameworkCore.Design
  Determining projects to restore...
  Writing /tmp/tmpBoLgId.tmp
info : Adding PackageReference for package 'Microsoft.EntityFrameworkCore.Design' into project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Restoring packages for /Projects/RazorPagesMovie/RazorPagesMovie.csproj...
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.design/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.design/index.json 890ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.design/3.1.6/microsoft.entityframeworkcore.design.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.design/3.1.6/microsoft.entityframeworkcore.design.3.1.6.nupkg 13ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.csharp/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.csharp/index.json 624ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.csharp/4.7.0/microsoft.csharp.4.7.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.csharp/4.7.0/microsoft.csharp.4.7.0.nupkg 12ms
info : Installing Microsoft.CSharp 4.7.0.
info : Installing Microsoft.EntityFrameworkCore.Design 3.1.6.
info : Package 'Microsoft.EntityFrameworkCore.Design' is compatible with all the specified frameworks in project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : PackageReference for package 'Microsoft.EntityFrameworkCore.Design' version '3.1.6' added to file '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Committing restore...
info : Generating MSBuild file /Projects/RazorPagesMovie/obj/RazorPagesMovie.csproj.nuget.g.props.
info : Writing assets file to disk. Path: /Projects/RazorPagesMovie/obj/project.assets.json
log  : Restored /Projects/RazorPagesMovie/RazorPagesMovie.csproj (in 2.15 sec).
```

短かった。最後 `Microsoft.EntityFrameCore.SqlServer`

```sh
# dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  Determining projects to restore...
  Writing /tmp/tmpiKEcFi.tmp
info : Adding PackageReference for package 'Microsoft.EntityFrameworkCore.SqlServer' into project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Restoring packages for /Projects/RazorPagesMovie/RazorPagesMovie.csproj...
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlserver/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlserver/index.json 827ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlserver/3.1.6/microsoft.entityframeworkcore.sqlserver.3.1.6.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.entityframeworkcore.sqlserver/3.1.6/microsoft.entityframeworkcore.sqlserver.3.1.6.nupkg 10ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlclient/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlclient/index.json 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlclient/1.0.19269.1/microsoft.data.sqlclient.1.0.19269.1.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.data.sqlclient/1.0.19269.1/microsoft.data.sqlclient.1.0.19269.1.nupkg 14ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.win32.registry/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.data.sqlclient.sni/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.principal.windows/index.json
info :   CACHE https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/4.5.0/system.text.encoding.codepages.4.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.configuration.configurationmanager/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.caching/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.identity.client/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.text.encoding.codepages/4.5.0/system.text.encoding.codepages.4.5.0.nupkg 70ms
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.win32.registry/index.json 177ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.win32.registry/4.5.0/microsoft.win32.registry.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.win32.registry/4.5.0/microsoft.win32.registry.4.5.0.nupkg 33ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.accesscontrol/index.json
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/2.0.0/microsoft.netcore.platforms.2.0.0.nupkg
info :   CACHE https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/4.5.0/system.runtime.compilerservices.unsafe.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.platforms/2.0.0/microsoft.netcore.platforms.2.0.0.nupkg 21ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.compilerservices.unsafe/4.5.0/system.runtime.compilerservices.unsafe.4.5.0.nupkg 70ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.data.sqlclient.sni/index.json 642ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.principal.windows/index.json 644ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.native.system.data.sqlclient.sni/4.4.0/runtime.native.system.data.sqlclient.sni.4.4.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.identity.client/index.json 641ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.configuration.configurationmanager/index.json 648ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.identity.client/3.0.8/microsoft.identity.client.3.0.8.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.caching/index.json 650ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.principal.windows/4.5.0/system.security.principal.windows.4.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.configuration.configurationmanager/4.5.0/system.configuration.configurationmanager.4.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.caching/4.5.0/system.runtime.caching.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.native.system.data.sqlclient.sni/4.4.0/runtime.native.system.data.sqlclient.sni.4.4.0.nupkg 17ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-arm64.runtime.native.system.data.sqlclient.sni/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-x64.runtime.native.system.data.sqlclient.sni/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-x86.runtime.native.system.data.sqlclient.sni/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.identity.client/3.0.8/microsoft.identity.client.3.0.8.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.principal.windows/4.5.0/system.security.principal.windows.4.5.0.nupkg 23ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.caching/4.5.0/system.runtime.caching.4.5.0.nupkg 22ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.configuration.configurationmanager/4.5.0/system.configuration.configurationmanager.4.5.0.nupkg 25ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.protecteddata/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.permissions/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.accesscontrol/index.json 600ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.accesscontrol/4.5.0/system.security.accesscontrol.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.accesscontrol/4.5.0/system.security.accesscontrol.4.5.0.nupkg 38ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-x64.runtime.native.system.data.sqlclient.sni/index.json 591ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-x86.runtime.native.system.data.sqlclient.sni/index.json 594ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-x64.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-x64.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-x86.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-x86.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-arm64.runtime.native.system.data.sqlclient.sni/index.json 605ms
info :   GET https://api.nuget.org/v3-flatcontainer/runtime.win-arm64.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-arm64.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-x64.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-x64.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg 32ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-x86.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-x86.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg 38ms
info :   OK https://api.nuget.org/v3-flatcontainer/runtime.win-arm64.runtime.native.system.data.sqlclient.sni/4.4.0/runtime.win-arm64.runtime.native.system.data.sqlclient.sni.4.4.0.nupkg 33ms
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.csharp/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.csharp/4.5.0/microsoft.csharp.4.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.typeconverter/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.nameresolution/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.formatters/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.json/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.primitives/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.securestring/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.csharp/4.5.0/microsoft.csharp.4.5.0.nupkg 27ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.permissions/index.json 594ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.protecteddata/index.json 596ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.cryptography.protecteddata/4.5.0/system.security.cryptography.protecteddata.4.5.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.permissions/4.5.0/system.security.permissions.4.5.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.permissions/4.5.0/system.security.permissions.4.5.0.nupkg 20ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.cryptography.protecteddata/4.5.0/system.security.cryptography.protecteddata.4.5.0.nupkg 26ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.nameresolution/index.json 584ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.net.nameresolution/4.3.0/system.net.nameresolution.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.typeconverter/index.json 589ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.json/index.json 587ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.typeconverter/4.3.0/system.componentmodel.typeconverter.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.json/4.3.0/system.runtime.serialization.json.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.net.nameresolution/4.3.0/system.net.nameresolution.4.3.0.nupkg 22ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.formatters/index.json 614ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.typeconverter/4.3.0/system.componentmodel.typeconverter.4.3.0.nupkg 25ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.formatters/4.3.0/system.runtime.serialization.formatters.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.json/4.3.0/system.runtime.serialization.json.4.3.0.nupkg 28ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.primitives/index.json 620ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.primitives/4.3.0/system.runtime.serialization.primitives.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.private.datacontractserialization/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.formatters/4.3.0/system.runtime.serialization.formatters.4.3.0.nupkg 27ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.nongeneric/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.specialized/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.primitives/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.runtime.serialization.primitives/4.3.0/system.runtime.serialization.primitives.4.3.0.nupkg 29ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.securestring/index.json 685ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.security.securestring/4.3.0/system.security.securestring.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.security.securestring/4.3.0/system.security.securestring.4.3.0.nupkg 33ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel/index.json 596ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.specialized/index.json 598ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.private.datacontractserialization/index.json 619ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.primitives/index.json 597ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.specialized/4.3.0/system.collections.specialized.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel.primitives/4.3.0/system.componentmodel.primitives.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.private.datacontractserialization/4.3.0/system.private.datacontractserialization.4.3.0.nupkg
info :   GET https://api.nuget.org/v3-flatcontainer/system.componentmodel/4.3.0/system.componentmodel.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.specialized/4.3.0/system.collections.specialized.4.3.0.nupkg 27ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel.primitives/4.3.0/system.componentmodel.primitives.4.3.0.nupkg 31ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.componentmodel/4.3.0/system.componentmodel.4.3.0.nupkg 32ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.nongeneric/index.json 641ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.collections.nongeneric/4.3.0/system.collections.nongeneric.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.private.datacontractserialization/4.3.0/system.private.datacontractserialization.4.3.0.nupkg 37ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.collections.nongeneric/4.3.0/system.collections.nongeneric.4.3.0.nupkg 38ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xmldocument/index.json
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xmlserializer/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xmlserializer/index.json 575ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xmlserializer/4.3.0/system.xml.xmlserializer.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xmldocument/index.json 581ms
info :   GET https://api.nuget.org/v3-flatcontainer/system.xml.xmldocument/4.3.0/system.xml.xmldocument.4.3.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xmldocument/4.3.0/system.xml.xmldocument.4.3.0.nupkg 9ms
info :   OK https://api.nuget.org/v3-flatcontainer/system.xml.xmlserializer/4.3.0/system.xml.xmlserializer.4.3.0.nupkg 14ms
info : Installing System.Xml.XmlSerializer 4.3.0.
info : Installing System.Xml.XmlDocument 4.3.0.
info : Installing System.Private.DataContractSerialization 4.3.0.
info : Installing System.Collections.NonGeneric 4.3.0.
info : Installing System.Collections.Specialized 4.3.0.
info : Installing System.ComponentModel 4.3.0.
info : Installing System.ComponentModel.Primitives 4.3.0.
info : Installing System.Runtime.Serialization.Json 4.3.0.
info : Installing System.ComponentModel.TypeConverter 4.3.0.
info : Installing System.Security.SecureString 4.3.0.
info : Installing System.Runtime.Serialization.Primitives 4.3.0.
info : Installing System.Runtime.Serialization.Formatters 4.3.0.
info : Installing System.Net.NameResolution 4.3.0.
info : Installing Microsoft.CSharp 4.5.0.
info : Installing System.Security.Cryptography.ProtectedData 4.5.0.
info : Installing System.Security.Permissions 4.5.0.
info : Installing Microsoft.EntityFrameworkCore.SqlServer 3.1.6.
info : Installing Microsoft.Data.SqlClient 1.0.19269.1.
info : Installing System.Runtime.Caching 4.5.0.
info : Installing System.Security.Principal.Windows 4.5.0.
info : Installing runtime.native.System.Data.SqlClient.sni 4.4.0.
info : Installing Microsoft.Win32.Registry 4.5.0.
info : Installing System.Text.Encoding.CodePages 4.5.0.
info : Installing Microsoft.Identity.Client 3.0.8.
info : Installing System.Runtime.CompilerServices.Unsafe 4.5.0.
info : Installing Microsoft.NETCore.Platforms 2.0.0.
info : Installing System.Configuration.ConfigurationManager 4.5.0.
info : Installing System.Security.AccessControl 4.5.0.
info : Installing runtime.win-x64.runtime.native.System.Data.SqlClient.sni 4.4.0.
info : Installing runtime.win-x86.runtime.native.System.Data.SqlClient.sni 4.4.0.
info : Installing runtime.win-arm64.runtime.native.System.Data.SqlClient.sni 4.4.0.
info : Package 'Microsoft.EntityFrameworkCore.SqlServer' is compatible with all the specified frameworks in project '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : PackageReference for package 'Microsoft.EntityFrameworkCore.SqlServer' version '3.1.6' added to file '/Projects/RazorPagesMovie/RazorPagesMovie.csproj'.
info : Committing restore...
info : Writing assets file to disk. Path: /Projects/RazorPagesMovie/obj/project.assets.json
log  : Restored /Projects/RazorPagesMovie/RazorPagesMovie.csproj (in 5.9 sec).
```

長かった。全てのインストールが終了した。

## プロジェクトファイルに必要なものを記述する

{% capture text %}

スキャフォールディング エラーが発生した場合は、ターゲット フレームワーク モニカー (TFM) がプロジェクト ファイル内の 
NuGet パッケージのバージョンと一致していることを確認します。 
たとえば、次のプロジェクト ファイルには、.NET Core のバージョン 3.1 と、一覧表示された NuGet パッケージが含まれています。

```XML
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.0" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.0" />
  </ItemGroup>

</Project>
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/tutorials/razor-pages/model?view=aspnetcore-3.1&tabs=visual-studio-code "パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する \| Microsoft Docs")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

_RazorPagesMovie.csproj_ がこうなっているらしいのだが、なんだか数が少ない。

```sh
$ cat RazorPagesMovie.csproj 
```

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.6">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.SQLite" Version="3.1.6" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.6" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.3" />
  </ItemGroup>

</Project>
```

ログを見ると、 _obj/project.asserts.json_ に何かが書き込まれている。確認してみる。

```sh
$ cat obj/project.assets.json
```

```json
~~~~略~~~~
      "netcoreapp3.1": {
        "dependencies": {
          "Microsoft.EntityFrameworkCore.Design": {
            "include": "Runtime, Build, Native, ContentFiles, Analyzers, BuildTransitive",
            "suppressParent": "All",
            "target": "Package",
            "version": "[3.1.6, )"
          },
          "Microsoft.EntityFrameworkCore.SQLite": {
            "target": "Package",
            "version": "[3.1.6, )"
          },
          "Microsoft.EntityFrameworkCore.SqlServer": {
            "target": "Package",
            "version": "[3.1.6, )"
          },
          "Microsoft.VisualStudio.Web.CodeGeneration.Design": {
            "target": "Package",
            "version": "[3.1.3, )"
          }
~~~~略~~~~
```

なんかが入っている感じはするのでこのまま進めてみる。