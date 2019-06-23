---
layout : post
title  : CMakeを使ってみる
date   : 2019/06/22
lastchange : 2019-06-23 05:55:13.
tags   :
  - CMake
  - C++
---

## いろんな使い方を読んでもサッパリなので

とりあえずチュートリアルをやってみることにする。


参考:

* [CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
* [CMakeの使い方（その１） - Qiita](https://qiita.com/shohirose/items/45fb49c6b429e8b204ac)
* [CMake 簡易まとめ - Qiita](https://qiita.com/janus_wel/items/a673793d448c72cbc95e)



## 最小限の CMake の設定ファイル

{% capture text %}

The most basic project is an executable built from source code files. 
For simple projects a two line CMakeLists.txt file is all that is required. 
This will be the starting point for our tutorial. The CMakeLists.txt file looks like:

```cmake
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
add_executable(Tutorial tutorial.cxx)
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

`CMakeLists.txt` の内容は次のようになります。

`CMakeLists.txt`

```cmake
# CMakeのバージョンを設定
cmake_minimum_required(VERSION 2.8)
# プロジェクト名と使用する言語を設定
project(test_cmake CXX)
# a.outという実行ファイルをmain.cppとhello.cppから作成
add_executable(a.out main.cpp hello.cpp)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMakeの使い方（その１） - Qiita](https://qiita.com/shohirose/items/45fb49c6b429e8b204ac)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



このように、 _CMakeLists.txt_ という設定ファイルを書き込んで `cmake` の動作を設定するようだ。
実際に動作させるサンプルとして、公式のチュートリアルを利用する。



{% capture text %}

The source code for tutorial.cxx will compute the square root of a number and the first version of it is very simple, as follows:

```cpp
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }
  double inputValue = atof(argv[1]);
  double outputValue = sqrt(inputValue);
  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}
```



{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



これを参考に、いろいろと古いので現状のバージョンなどに書き換える。
まずは `brew` に入っているバージョンを確認する。

```bash
$ cmake --version
cmake version 3.14.5

CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

これを元にバージョン情報を書き換える。

_CMakeLists.txt_

```cmake
cmake_minimum_required(VERSION 3.14)
project(Tutorial)
add_executable(Tutorial main.cpp)
```

C++ の記述も最新のヘッダファイルに変更。

_main.cpp_

```cpp
#include <cstdio>
#include <cstdlib>
#include <cmath>

int main ( int argc, char* argv[] ) {

  if( argc < 2 ) { 
    fprintf( stdout, "Usage: %s number\n ", argv[ 0 ] );
    return 1;
  }


  double inputValue  = atof( argv[ 1 ] );
  double outputValue = sqrt( inputValue );

  fprintf( stdout, "The square root of %g is %g\n", inputValue, outputValue );

  return 0;
}
```


## CMake を実行する

さて、早速CMake を実行してみたいのだが




{% capture text %}

CMakeを使ってビルドするときは、次のように必ずソースディレクトリとは別にビルド専用のディレクトリを作成し、その中でビルドします。

```bash
> mkdir build
> cd build
> cmake ..
> make
```

最終的なディレクトリ構成は以下のようになっているはずです。

```
---/
 |-CMakeLists.txt
 |-main.cpp
 |-hello.hpp
 |-hello.cpp
 |-build/
    |-a.out
    |...（その他色々）
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMakeの使い方（その１） - Qiita](https://qiita.com/shohirose/items/45fb49c6b429e8b204ac)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

その他のファイルと混じらないよう、ビルド用のディレクトリーを作成してその中で走らせるのが定石 ?
/path/to/your/sources/ に CMakeLists.txt が存在すると仮定する。

```bash
# ビルド時コマンド
cd /path/to/your/sources
mkdir build
cd build
cmake ..
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake 簡易まとめ - Qiita](https://qiita.com/janus_wel/items/a673793d448c72cbc95e)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


_build_ ディレクトリを作ってその中でビルドするのがいいらしい。

```sh
$ mkdir build
$ cd build 
$ cmake ..
-- The C compiler identification is AppleClang 10.0.0.10001145
-- The CXX compiler identification is AppleClang 10.0.0.10001145
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/leico_studio/pro/C++/cmake/build
```


このような画面が出て、ビルドするためのファイルが作成されたようだ。なので `ls` してみる。

```sh
$ ls -l
total 56
-rw-r--r--   1 leico_studio  staff  13731  6 23 04:22 CMakeCache.txt
drwxr-xr-x  15 leico_studio  staff    480  6 23 04:23 CMakeFiles
-rw-r--r--   1 leico_studio  staff   4942  6 23 04:22 Makefile
-rw-r--r--   1 leico_studio  staff   1391  6 23 04:22 cmake_install.cmake
```

このようなファイルができていた。しかし肝心の実行ファイルはこの時点で存在しない。
`cmake` が生み出すのは _Makefile_ まで。ここからさらに `make` すると目的の実行ファイルが作られる。

```sh
$ make
Scanning dependencies of target Tutorial
[ 50%] Building CXX object CMakeFiles/Tutorial.dir/main.cpp.o
[100%] Linking CXX executable Tutorial
```

実行ファイルができたので実行してみる。

```sh
$ ./Tutorial 25
The square root of 25 is 5
$ ./Tutorial 
Usage: ./Tutorial number
```


