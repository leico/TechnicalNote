---
layout : post
title  : ライブラリとリンクする
date   : 2019/06/23
lastchange : 2019-06-24 23:46:01.
tags   :
  - CMake
  - C++
---

## ライブラリとリンクする

参考:

* [CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)


### ライブラリを作る

{% capture text %}

Now we will add a library to our project. 
This library will contain our own implementation for computing the square root of a number. 
The executable can then use this library instead of the standard square root function provided by the compiler.
For this tutorial we will put the library into a subdirectory called MathFunctions. 
It will have the following one line _CMakeLists.txt_ file:

```cmake
add_library(MathFunctions mysqrt.cxx)
```

The source file _mysqrt.cxx_ has one function called mysqrt 
that provides similar functionality to the compiler’s sqrt function.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`mysqrt` という関数を作る。それは _mysqrt.cpp_ というファイルに書かれていて、
それ自体は _MathFunctions_ という名前のサブディレクトリに入っているらしい。
さらに、 _MathFunctions_ サブディレクトリ内にも _CMakeLists.txt_ が入っているらしい。
ここまでをファイル構造に反映させる。

```bash
|--CMakeLists.txt
|--MathFunctions
|  |--CMakeLists.txt
|  |--mysqrt.cpp
|--TutorialConfig.h.in
|--main.cpp
```

_MathFunctions/CMakeLists.txt_ 内部は上記の通り、ファイル名を変えているので反映させる。

```cmake
add_library(MathFunctions mysqrt.cpp)
```




{% capture text %}

To make use of the new library we add an add_subdirectory call in the top level _CMakeLists.txt_ file 
so that the library will get built. We also add another include directory 
so that the _MathFunctions/MathFunctions.h_ header file can be found for the function prototype. 
The last change is to add the new library to the executable. 
The last few lines of the top level _CMakeLists.txt_ file now look like:

```cmake
include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
add_subdirectory (MathFunctions) 
 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial MathFunctions)
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


このライブラリの関数のプロトタイプが記述されているヘッダファイル、 _MathFunctions.h_ も _MathFunctions_
ディレクトリの中に作成する。ここまでのファイル構造。

```bash
|--CMakeLists.txt
|--MathFunctions
|  |--CMakeLists.txt
|  |--MathFunctions.h
|  |--mysqrt.cpp
|--TutorialConfig.h.in
|--main.cpp
```

そして、 _mysqrt.cpp_ の中身と

```cpp
#include <cmath>
#include "MathFunctions.h"

double mysqrt( double value ){ 
  return sqrt( value );
}

```

_MathFunctions.h_ の中身となる。

```cpp

double mysqrt( double value );

```


ライブラリ側の準備ができたので、プロジェクト自体の _CMakeLists.txt_ を変更する。


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
include_directories("${PROJECT_BINARY_DIR}/MathFunctions")
add_subdirectory(MathFunctions)

add_executable(Tutorial main.cpp)
target_link_libraries(Tutorial MathFunctions)
```



同時に、 _main.cpp_ も変更する。



```cpp
#include <cstdio>
#include <cstdlib>
#include <cmath>
#include "TutorialConfig.h"
#include "MathFunctions.h" 

int main ( int argc, char* argv[] ) {

  fprintf( stdout, "%s ver. %d.%d\n", argv[ 0 ], Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR );

  if( argc < 2 ) { 
    fprintf( stdout, "Usage: %s number\n ", argv[ 0 ] );
    return 1;
  }


  double inputValue  = atof( argv[ 1 ] );
  double outputValue = mysqrt( inputValue );

  fprintf( stdout, "The square root of %g is %g\n", inputValue, outputValue );

  return 0;
}
```



`#include"MathFunctions.h"` が追加され、平方根を求めるプログラムが
`double outputValue = mysqrt( inputValue );` に変更されている。

これで cmake を実行すると

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
Scanning dependencies of target MathFunctions
[ 25%] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.o
[ 50%] Linking CXX static library libMathFunctions.a
[ 50%] Built target MathFunctions
Scanning dependencies of target Tutorial
[ 75%] Building CXX object CMakeFiles/Tutorial.dir/main.cpp.o
[100%] Linking CXX executable Tutorial
[100%] Built target Tutorial
./Tutorial ver. 1.0
The square root of 144 is 12
```


無事コンパイルが通った。 _build/MathFunctions_ ディレクトリが中にできているので覗いてみると


```bash
|--CMakeFiles
|  |--CMakeDirectoryInformation.cmake
|  |--MathFunctions.dir
|  |  |--CXX.includecache
|  |  |--DependInfo.cmake
|  |  |--build.make
|  |  |--cmake_clean.cmake
|  |  |--cmake_clean_target.cmake
|  |  |--depend.internal
|  |  |--depend.make
|  |  |--flags.make
|  |  |--link.txt
|  |  |--mysqrt.cpp.o
|  |  |--progress.make
|  |--progress.marks
|--Makefile
|--cmake_install.cmake
|--libMathFunctions.a
```


_libMathFunctions.a_ というライブラリが生成されていることがわかる。
この生成されたライブラリを使ってプログラムをビルドしたことになる。
