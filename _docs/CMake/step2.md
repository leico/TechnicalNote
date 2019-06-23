---
layout : post
title  : CMake で変数を利用する
date   : 2019/06/23
lastchange : 2019-06-23 20:43:49.
tags   :
  - CMake
  - C++
---

## 変数に関して少し詳しく

チュートリアルの内容が、次に利用するのが変数だったので。

参考:

* [CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
* [CMake覚え書き - Qiita](https://qiita.com/progrhyme/items/c0f21e2a71cfe6fdb7a8)
* [CMake: 便利なコマンド・変数 - Qiita](https://qiita.com/mrk_21/items/5e7ca775b463a4141a58)
* [CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
* [CMakeを使ってみた（2）もう少しまともなプロジェクト - wagavulin's blog](https://www.wagavulin.jp/entry/2011/11/27/222642)
* [CMakeで設定されているすべての変数を出力する - 座敷牢日誌](http://zashikiro.hateblo.jp/entry/2014/05/17/001314)
  * [CMAKE: Print out all accessible variables in a script - Stack Overflow](http://stackoverflow.com/questions/9298278/cmake-print-out-all-accessible-variables-in-a-script)


## 変数を利用する


{% capture text %}

### Adding a Version Number and Configured Header File

The first feature we will add is to provide our executable and project with a version number. 
While you can do this exclusively in the source code, doing it in the CMakeLists.txt file provides more flexibility. 
To add a version number we modify the CMakeLists.txt file as follows:

```cmake
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
# The version number.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
 
# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
 
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories("${PROJECT_BINARY_DIR}")
 
# add the executable
add_executable(Tutorial tutorial.cxx)
```
 

Since the configured file will be written into the binary tree we must add that 
directory to the list of paths to search for include files. 
We then create a TutorialConfig.h.in file in the source tree with the following contents:

```cpp
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




いきなり変数がいくつか出てきている。そして、その変数をビルドするソース内のヘッダなどに埋め込めるということらしいが、
変数の中身がわからないのがあるのでこれを確認したい。


## 変数をログ出力する


{% capture text %}

## message

https://cmake.org/cmake/help/v3.8/command/message.html

ログメッセージを出力する関数。

```cmake
message([<mode>] "message to display" ...)
```

省略可能な `<mode>` には以下のようなものがある（一部）:

| mode        | description  | 処理の継続 | 出力先 |
|-------------|--------------|------------|--------|
| (none)      | 重要な情報   | Yes        | STDERR |
| STATUS      | INFO的なやつ | Yes        | STDOUT |
| WARNING     | 警告         | Yes        | STDERR |
| FATAL_ERROR | 致命的エラー | No         | STDERR |

出力先は、STATUS以外は `STDOUT` である。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake覚え書き - Qiita](https://qiita.com/progrhyme/items/c0f21e2a71cfe6fdb7a8)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




最後 `STDOUT` は `STDERR` の間違いのようだが、これで出力することができるようだ。
で、どのように変数をこの関数に渡してあげればよいのか。





{% capture text %}

## CMAKE_COMMAND・CMAKE_CTEST_COMMAND 変数

これらの変数は、cmakeコマンドやctestコマンドのフルパスを返します。

```cmake
message("CMAKE_COMMAND: ${CMAKE_COMMAND}") # /usr/bin/cmake
message("CMAKE_CTEST_COMMAND: ${CMAKE_CTEST_COMMAND}") # /usr/bin/ctest
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 便利なコマンド・変数 - Qiita](https://qiita.com/mrk_21/items/5e7ca775b463a4141a58)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% capture text %}

## 変数の値の参照

変数の値の参照は、`${variable-name}` という構文で行います。

```cmake
set(value 1)
message(${value}) # 1
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




普通に `${Variable}` で表示してくれるようだ。なのでこんな _CMakeLists.txt_ を作って実行してみる。

```cmake
cmake_minimum_required(VERSION 3.14)


message(STATUS "PROJECT_SOURCE_DIR = ${PROJECT_SOURCE_DIR}")
message(STATUS "PROJECT_BINARY_DIR = ${PROJECT_BINARY_DIR}")
```


```bash
$ mkdir build && cd build && cmake ..
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
-- PROJECT_SOURCE_DIR = /Users/leico_studio/pro/C++/cmake
-- PROJECT_BINARY_DIR = /Users/leico_studio/pro/C++/cmake/build
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/leico_studio/pro/C++/cmake/build
```

内容が判明したので、このチュートリアルで利用されている変数についてまとめた。

PROJECT_SOURCE_DIR
: ソースディレクトリのフルパス

PROJECT_BINARY_DIR
: ビルドしたものを出力するディレクトリのフルパス

Tutorial_VERSION_MAJOR
: ソフトのメジャーバージョン

Tutorial_VERSION_MINOR
: ソフトのマイナーバージョン



## config用ヘッダファイルの作成と置き場所、変数の利用


変数に格納されているパスが確定したので、その場所、 `PROJECT_SOURCE_DIR` に config 用ヘッダファイル _TutorialConfig.h.in_ を置く。
内容は上記の通り、


```cpp
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

config 用ヘッダファイル内で `cmake` の変数を利用するには、上記の通り `@Variable@` という記述となるようだ。
ここまでを踏まえて、以下の内容で `cmake` の設定などを書き換えて実際に動かしてみた。

## チュートリアルの実行

### PROJECT_SOURCE_DIR ファイル構成

```bash
$ ls -l
total 24
-rw-r--r--  1 leico_studio  staff  307  6 23 20:08 CMakeLists.txt
-rw-r--r--  1 leico_studio  staff  165  6 23 20:01 TutorialConfig.h.in
-rw-r--r--  1 leico_studio  staff  499  6 23 20:10 main.cpp
```

### _TutorialConfig.h.in_

```cpp

// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

### main.cpp

```cpp
#include <cstdio>
#include <cstdlib>
#include <cmath>
#include "TutorialConfig.h"

int main ( int argc, char* argv[] ) {

  fprintf( stdout, "%s ver. %d.%d\n", argv[ 0 ], Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR );

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

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)

project(Tutorial)

set(Tutorial_VERSION_MAJOR 1)
set(Tutorial_VERSION_MINOR 0)

configure_file(
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
)


include_directories("${PROJECT_BINARY_DIR}")

add_executable(Tutorial main.cpp)
```


### 実行結果

```bash
$ mkdir build && cd build && cmake .. && make && ./Tutorial 144
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
Scanning dependencies of target Tutorial
[ 50%] Building CXX object CMakeFiles/Tutorial.dir/main.cpp.o
[100%] Linking CXX executable Tutorial
[100%] Built target Tutorial
./Tutorial ver. 1.0
The square root of 144 is 12
```

1. _build_ ディレクトリの作成
2. _build_ ディレクトリへの移動
3. `cmake` 実行、 `make` 実行
4. 生成されたプログラム `Tutorial` の実行

を1行でやってしまっているのでちょっとわかりにくいか。


### build ディレクトリの中身

```bash
$ ls -l
total 88
-rw-r--r--   1 leico_studio  staff  13731  6 23 20:10 CMakeCache.txt
drwxr-xr-x  15 leico_studio  staff    480  6 23 20:10 CMakeFiles
-rw-r--r--   1 leico_studio  staff   4942  6 23 20:10 Makefile
-rwxr-xr-x   1 leico_studio  staff   8532  6 23 20:10 Tutorial
-rw-r--r--   1 leico_studio  staff    119  6 23 20:10 TutorialConfig.h
-rw-r--r--   1 leico_studio  staff   1391  6 23 20:10 cmake_install.cmake
```

_Makefile_ などがある以外に、 _TutorialConfig.h_ が _TutorialConfig.h.in_ から生成されているようだ。
_TutorialConfig.h_ の中身を確認してみる

```cpp
$ cat TutorialConfig.h 

// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR 1
#define Tutorial_VERSION_MINOR 0
```

各々の値に書き換わっているのが確認できる。


以下、ここまでで触れることができなかったものに遭遇したのでまとめておく。









## ヘッダファイルを捜索する場所を指定する

{% capture text %}


#### インクルードパスを指定する

インクルードパスを指定するときは `include_directories` コマンドを使う。

```cmake
cmake_minimum_required(VERSION 2.8)
include_directories(/path/to/include)
add_executable(myapp main.cpp)
```

なお、 `include_directories` は複数回指定可能で、デフォルトでは後で指定したものは後ろにつく。
ただし、オプションで `BEFORE` を指定したときは前につく。例えば、

```cmake
include_directories(/path1/include)
include_directories(/path2/include)
```

としたときは `"-I/path1/include -I/path2/include"` となり、

```cmake
include_directories(/path1/include)
include_directories(BEFORE /path2/include)
```

としたときは `"-I/path2/include -I/path1/include"` となる。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMakeを使ってみた（2）もう少しまともなプロジェクト - wagavulin's blog](https://www.wagavulin.jp/entry/2011/11/27/222642)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






## 変数についての関数

### 変数に値を入れる、消す


{% capture text %}

## 変数の生成・代入

変数は、`set()` コマンドを用いて生成することができます。

```cmake
set(value 1) # 値が1の変数valueを生成
```


また、既存の変数への代入もset()コマンドを用います。


```cmake
set(value 1) # 値が1の変数valueを生成
set(value 2) # 既存の変数valueに値1を代入
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





また、





{% capture text %}

変数の値の参照は、入れ子にすることもできます。

```cmake
set(value 1)
set(value_name value)
message(${${value_name}}) # 1
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






これは

```cmake

set(value 1)          # value      に 1     をセット
set(value_name value) # value_name に value をセット
set(${${value_name}}) 
# ${value_name} が展開され ${${value_name}} が ${value} になり、これが展開されて 1 になる
```


ということのようだ。

さらに波括弧の中では





{% capture text %}

組み合わせることもできます

```cmake
set(value_10 1)
set(value_basename value)
set(value_no 10)
message(${${value_basename}_${value_no}}) # 1
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





他、定義していない変数を利用した場合は






{% capture text %}


空文字列が返されます。

```cmake
message("not_existing_value: '${not_existing_value}'") # not_existing_value: ''
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}







## 変数の削除

同じページに削除する方法も載っていた

{% capture text %}

## 変数の削除

変数の削除には、 `unset()` コマンドを用います。

```cmake
set(value 1) # 変数valueを生成
unset(value) # 変数valueを削除
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake: 変数 - Qiita](https://qiita.com/mrk_21/items/68470da5d1931915cde2)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




## 全ての定義された変数を出力する



{% capture text %}

CMakeのなかで使われている変数はたくさんあって 
(よく使うのは `CMAKE_CXX_FLAGS` とかですかね)、
それらをすべて出力する方法はないかと思って調べてみたら、stackoverflowにあった。

[CMAKE: Print out all accessible variables in a script - Stack Overflow](http://stackoverflow.com/questions/9298278/cmake-print-out-all-accessible-variables-in-a-script)

_CMakeLists.txt_ に次のコードを書いて実行すると、 `*** dump start cmake variables ***`
と  `*** dump end ***` の間にすべての変数が出力される。

```cmake
message(STATUS "*** dump start cmake variables ***")
get_cmake_property(_variableNames VARIABLES)
foreach(_variableName ${_variableNames})
        message(STATUS "${_variableName}=${${_variableName}}")
endforeach()
message(STATUS "*** dump end ***")
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMakeで設定されているすべての変数を出力する - 座敷牢日誌](http://zashikiro.hateblo.jp/entry/2014/05/17/001314)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

