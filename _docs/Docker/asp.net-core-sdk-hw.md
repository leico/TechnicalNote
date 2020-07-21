---
layout : post
title  : "ASP.NET Core SDK コンテナで動作しているプログラムを変更する"
date   : 2020/07/21
lastchange : 2020-07-21 16:28:54
tags   :
  - docker
  - compose
  - container
  - ASP.NET Core
  - .NET SDK
  - webApp
---

## Microsoft が提供しているチュートリアルを先にすすめる

前回、
[ASP.NET Core SDK コンテナにディレクトリをマウントする]({{site.github.url}}{% link _docs/Docker/asp.net-core-sdk-mount.md %})
ではディレクトリをマウントすることで、ホスト側でソースの変更ができ、ホスト側にアプリのデータを残せるようになった。

なので今回は Microsoft のチュートリアル
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/ "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
を最後まで進める。

参考:

* [ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/ "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")


## ファイルを変更する

{% capture text %}
### Edit your code

Open `Pages/Index.cshtml` in any text editor and replace all of the code with the following, then save the file.

```cshtml
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div class="text-center">
    <h1>Hello, world!</h1>
    <p>The time on the server is @DateTime.Now</p>
</div>
```

### Re-run your app

End the previous `dotnet run` command that is running the site locally,
then run the following command to re-launch the site:

```sh
dotnet run
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ASP.NET Tutorial \| Hello World in 10 minutes \| .NET](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/modify "ASP.NET Tutorial \| Hello World in 10 minutes \| .NET")
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

やってみる。まずは donnet SDK コンテナを動作させる。

```sh
$ ./run_SDK_container.sh 
/ # 
```

起動した。別のターミナルからホスト側で `Pages/Index.cshtml` を編集する。

```sh
$ vimr WebApp/Pages/Index.cshtml
```

```cshtml
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
    <p>この行を追加した。</p>
</div>
```

変更した。コンテナ側で WebApp を動作させてみる。

```sh
# cd home/WebApp/
# dotnet run
warn: Microsoft.AspNetCore.DataProtection.Repositories.FileSystemXmlRepository[60]
      Storing keys in a directory '/root/.aspnet/DataProtection-Keys' that may not be persisted outside of the container. Protected data will be unavailable when container is destroyed.
warn: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[35]
      No XML encryptor configured. Key {1a5b21f9-802c-405d-a620-1a5c239e4953} may be persisted to storage in unencrypted form.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://0.0.0.0:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /home/WebApp
warn: Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionMiddleware[3]
      Failed to determine the https port for redirect.
```

動作したらしい。ホスト側から <http://localhost:5000> へアクセスして確認する。

{% capture url %}{{site.github.url}}{% link _docs/Docker/images/asp.net-core-sdk-hw/01_modify.png %}{% endcapture %}
{% capture caption %}
無事に変更が反映された
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}

