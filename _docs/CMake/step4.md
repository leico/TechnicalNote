---
layout : post
title  : config header で使用するライブラリを切り替える
date   : 2019/06/26
lastchange : 2019-07-01 11:56:22.
tags   :
  - CMake
  - C++
---

## 利用するライブラリを config header で切り替える

参考:

* [CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
* [CMake覚え書き - Qiita](https://qiita.com/progrhyme/items/c0f21e2a71cfe6fdb7a8#option)
* [cmakeプロジェクトの設定項目一覧を表示させる - かみのメモ](https://kamino.hatenablog.com/entry/cmake-list-options)
* [CMakeを使ってみた (4) ビルドオプション - wagavulin's blog](https://www.wagavulin.jp/entry/2011/11/27/222650)
* [CMake:configure_fileコマンド - Qiita](https://qiita.com/osamu0329/items/edc66e2e1b6c96947771)




### オプションを設定する

{% capture text %}

Now let us consider making the MathFunctions library optional. 
In this tutorial there really isn’t any reason to do so, 
but with larger libraries or libraries that rely on third party code you might want to. 
The first step is to add an option to the top level CMakeLists.txt file.

```cmake
# should we use our own math functions?
option (USE_MYMATH 
        "Use tutorial provided math implementation" ON) 
```

This will show up in the CMake GUI with a default value of ON that the user can change as desired. 
This setting will be stored in the cache so that 
the user does not need to keep setting it each time they run CMake on this project. 

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}







{% capture text %}
## option

https://cmake.org/cmake/help/v3.8/command/option.html

```cmake
option(<option_variable> "help string describing option" [ON or OFF])
```

optionを指定するときは、cmakeコマンド実行時に `-D<option_variable>=ON` などとして渡す。


例:

```cmake
# shell
# FOOオプションをONでビルド実行
cmake -DFOO=ON path/to/project_dir
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake覚え書き - Qiita](https://qiita.com/progrhyme/items/c0f21e2a71cfe6fdb7a8#option)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



`option` によって実行時にのオプションを指定することができるようだ。
このように指定されたオプションの一覧を確認するには





{% capture text %}

## cmakeコマンドから

cmakeコマンドを使うときは `cmake .. -L` のように `-L` オプションを付けると、configurationの後にキャッシュ変数の一覧が表示されます。

```cmake
-- Cache values
BUILD_SHARED_LIBS:BOOL=OFF
CMAKE_BUILD_TYPE:STRING=
CMAKE_INSTALL_PREFIX:PATH=/usr/local
CMAKE_LIBTOOL:FILEPATH=/usr/bin/libtool
...
```

`-LH` とすれば、変数に紐付けられているコメントも一緒に表示されます。

```cmake
-- Cache values
// Build shared libraries
BUILD_SHARED_LIBS:BOOL=OFF

// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/usr/local

...
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[cmakeプロジェクトの設定項目一覧を表示させる - かみのメモ](https://kamino.hatenablog.com/entry/cmake-list-options)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



ここまでを設定して確認してみる。


直下の _CMakeLists.txt_ を変更

```cmake

cmake_minimum_required(VERSION 3.14)

project(Tutorial)

set(Tutorial_VERSION_MAJOR 1)
set(Tutorial_VERSION_MINOR 0)


option(USE_MYMATH "Use tutorial provided math implementation" OFF)

configure_file(
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
)


include_directories("${PROJECT_BINARY_DIR}")
include_directories("${PROJECT_SOURCE_DIR}/MathFunctions")
add_subdirectory(MathFunctions)
set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)

add_executable(Tutorial main.cpp)
target_link_libraries(Tutorial MathFunctions)

```


実行結果




```sh
$ mkdir build && cd build && cmake -LH .. 
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=OFF


```

オプション値見るにもさまざまなファイルが自動的に生成されるので、
普通にビルドする時と同様、 _build_ ディレクトリを作成してその中で `cmake` するほうがよい。

実際に設定されているオプション値が一覧で表示された。オプションの値を設定しているのがわかりやすくするため、
チュートリアルから変更してデフォルトの値を `OFF` としている。

### オプションの値を設定する


{% capture text %}

cmakeの初回実行で、何も指定しない -> 初期値 (ON) になる。

```sh
> cmake . && make && ./myapp
(snip)
feature-x enabled
```

コマンドラインオプションでOFFにする。

```sh
> cmake -DUSE_FEATURE_X=OFF . && make && ./myapp
(snip)
feature-x disabled
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMakeを使ってみた (4) ビルドオプション - wagavulin's blog](https://www.wagavulin.jp/entry/2011/11/27/222650)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

このように引数で指定する。


```bash
$ mkdir build && cd build && cmake -DUSE_MYMATH=ON -LH .. 
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=ON
```

### オプションの値を config 内で利用する

{% capture text %}

The next change is to make the build and linking of the MathFunctions library conditional. To do this we change the end of the top level CMakeLists.txt file to look like the following:

```cmake
# add the MathFunctions library?
#
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)
 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})
```

This uses the setting of USE_MYMATH to determine if the MathFunctions should be compiled and used. Note the use of a variable (EXTRA_LIBS in this case) to collect up any optional libraries to later be linked into the executable. This is a common approach used to keep larger projects with many optional components clean.


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


チュートリアルでは `USE_MYMATH` の値次第で MathFunctions ライブラリをリンクさせるかを決めている。
実際に _CMakeLists.txt_ を変更してやってみる。

```cmake
cmake_minimum_required(VERSION 3.14)

project(Tutorial)

set(Tutorial_VERSION_MAJOR 1)
set(Tutorial_VERSION_MINOR 0)


option(USE_MYMATH "Use tutorial provided math implementation" OFF)

configure_file(
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
)


include_directories("${PROJECT_BINARY_DIR}")


if(USE_MYMATH)
  include_directories("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory(MathFunctions)
  set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif(USE_MYMATH)


add_executable(Tutorial main.cpp)
target_link_libraries(Tutorial ${EXTRA_LIBS})
```


`USE_MYMATH` の有効/無効を切り替えて実行してみる。


#### 無効時の結果

```bash
$ mkdir build && cd build && cmake -LH .. 
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=OFF

```


```
|--CMakeCache.txt
|--CMakeFiles
|  |--3.14.5
|  |  |--CMakeCCompiler.cmake
|  |  |--CMakeCXXCompiler.cmake
|  |  |--CMakeDetermineCompilerABI_C.bin
|  |  |--CMakeDetermineCompilerABI_CXX.bin
|  |  |--CMakeSystem.cmake
|  |  |--CompilerIdC
|  |  |  |--CMakeCCompilerId.c
|  |  |  |--a.out
|  |  |  |--tmp
|  |  |--CompilerIdCXX
|  |  |  |--CMakeCXXCompilerId.cpp
|  |  |  |--a.out
|  |  |  |--tmp
|  |--CMakeDirectoryInformation.cmake
|  |--CMakeOutput.log
|  |--CMakeTmp
|  |--Makefile.cmake
|  |--Makefile2
|  |--TargetDirectories.txt
|  |--Tutorial.dir
|  |  |--DependInfo.cmake
|  |  |--build.make
|  |  |--cmake_clean.cmake
|  |  |--depend.make
|  |  |--flags.make
|  |  |--link.txt
|  |  |--progress.make
|  |--cmake.check_cache
|  |--feature_tests.bin
|  |--feature_tests.c
|  |--feature_tests.cxx
|  |--progress.marks
|--Makefile
|--TutorialConfig.h
|--cmake_install.cmake
```




#### 有効時の結果

```bash
&& cd build && cmake -DUSE_MYMATH=ON -LH .. 
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=ON
```



```
|--CMakeCache.txt
|--CMakeFiles
|  |--3.14.5
|  |  |--CMakeCCompiler.cmake
|  |  |--CMakeCXXCompiler.cmake
|  |  |--CMakeDetermineCompilerABI_C.bin
|  |  |--CMakeDetermineCompilerABI_CXX.bin
|  |  |--CMakeSystem.cmake
|  |  |--CompilerIdC
|  |  |  |--CMakeCCompilerId.c
|  |  |  |--a.out
|  |  |  |--tmp
|  |  |--CompilerIdCXX
|  |  |  |--CMakeCXXCompilerId.cpp
|  |  |  |--a.out
|  |  |  |--tmp
|  |--CMakeDirectoryInformation.cmake
|  |--CMakeOutput.log
|  |--CMakeTmp
|  |--Makefile.cmake
|  |--Makefile2
|  |--TargetDirectories.txt
|  |--Tutorial.dir
|  |  |--DependInfo.cmake
|  |  |--build.make
|  |  |--cmake_clean.cmake
|  |  |--depend.make
|  |  |--flags.make
|  |  |--link.txt
|  |  |--progress.make
|  |--cmake.check_cache
|  |--feature_tests.bin
|  |--feature_tests.c
|  |--feature_tests.cxx
|  |--progress.marks
|--Makefile
|--MathFunctions
|  |--CMakeFiles
|  |  |--CMakeDirectoryInformation.cmake
|  |  |--MathFunctions.dir
|  |  |  |--DependInfo.cmake
|  |  |  |--build.make
|  |  |  |--cmake_clean.cmake
|  |  |  |--cmake_clean_target.cmake
|  |  |  |--depend.make
|  |  |  |--flags.make
|  |  |  |--link.txt
|  |  |  |--progress.make
|  |  |--progress.marks
|  |--Makefile
|  |--cmake_install.cmake
|--TutorialConfig.h
|--cmake_install.cmake

```


有効にした時に MathFunctions 関係のものが増えているのがわかる。


### オプションの変更をソースコード側に反映させる

{% capture text %}

his is provided from CMake to the source code through the TutorialConfig.h.in configured file by adding the following line to it:

```cmake
#cmakedefine USE_MYMATH
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake Tutorial \| CMake](https://cmake.org/cmake-tutorial/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}
### Cプリプロセッサ向け機能

入力ファイル中の `#cmakedefine VAR ...` の形式の行は、`#define VAR ...` か `/* #undef VAR */` の
どちらかに置き換えられます。CMake変数のVAR が `if()` コマンドで false と評価される場合は、
`/* #undef VAR */` に、さもなければ `#define VAR ...` に置き換えられます。
`...` の部分は、通常と同様のルールで置換されます。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[CMake:configure_fileコマンド - Qiita](https://qiita.com/osamu0329/items/edc66e2e1b6c96947771)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


つまり、 `cmake` 実行時

|                           | `USE_MYMATH = ON`    | `USE_MYMATH = OFF`        |
|---------------------------|----------------------|---------------------------|
| `#cmakedefine USE_MYMATH` | `#define USE_MYMATH` | `/* #undef USE_MYMATH */` |

というように、オプションによって `#cmakedefne` の部分が置き換えられてくれる。
オプションの値によってマクロが変化し、ソースコードの内容を変更することができる。

これを反映させる。

_TutorialConfig.h.in_

```cmake
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@


#cmakedefine USE_MYMATH
```

_main.cpp_

```cpp
#include <cstdio>
#include <cstdlib>
#include <cmath>
#include "TutorialConfig.h"

#ifdef USE_MYMATH
  #include "MathFunctions.h"
#endif

int main ( int argc, char* argv[] ) {

  fprintf( stdout, "%s ver. %d.%d\n", argv[ 0 ], Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR );

  if( argc < 2 ) { 
    fprintf( stdout, "Usage: %s number\n ", argv[ 0 ] );
    return 1;
  }


  double inputValue  = atof( argv[ 1 ] );

#ifdef USE_MYMATH
  fprintf( stdout, "mysqrt\n");
  double outputValue = mysqrt( inputValue );
#else
  fprintf( stdout, "sqrt\n");
  double outputValue = sqrt(inputValue);
#endif

  fprintf( stdout, "The square root of %g is %g\n", inputValue, outputValue );

  return 0;
}
```





#### 無効時の結果

```bash
$ mkdir build && cd build && cmake -LH .. && make && ./Tutorial 144
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=OFF

Scanning dependencies of target Tutorial
[ 50%] Building CXX object CMakeFiles/Tutorial.dir/main.cpp.o
[100%] Linking CXX executable Tutorial
[100%] Built target Tutorial
./Tutorial ver. 1.0
sqrt
The square root of 144 is 12
```


```bash
|--CMakeCache.txt
|--CMakeFiles
|  |--3.14.5
|  |  |--CMakeCCompiler.cmake
|  |  |--CMakeCXXCompiler.cmake
|  |  |--CMakeDetermineCompilerABI_C.bin
|  |  |--CMakeDetermineCompilerABI_CXX.bin
|  |  |--CMakeSystem.cmake
|  |  |--CompilerIdC
|  |  |  |--CMakeCCompilerId.c
|  |  |  |--a.out
|  |  |  |--tmp
|  |  |--CompilerIdCXX
|  |  |  |--CMakeCXXCompilerId.cpp
|  |  |  |--a.out
|  |  |  |--tmp
|  |--CMakeDirectoryInformation.cmake
|  |--CMakeOutput.log
|  |--CMakeTmp
|  |--Makefile.cmake
|  |--Makefile2
|  |--TargetDirectories.txt
|  |--Tutorial.dir
|  |  |--CXX.includecache
|  |  |--DependInfo.cmake
|  |  |--build.make
|  |  |--cmake_clean.cmake
|  |  |--depend.internal
|  |  |--depend.make
|  |  |--flags.make
|  |  |--link.txt
|  |  |--main.cpp.o
|  |  |--progress.make
|  |--cmake.check_cache
|  |--feature_tests.bin
|  |--feature_tests.c
|  |--feature_tests.cxx
|  |--progress.marks
|--Makefile
|--Tutorial
|--TutorialConfig.h
|--cmake_install.cmake
```


#### 有効時の結果

```bash
$ mkdir build && cd build && cmake -DUSE_MYMATH=ON -LH .. && make && ./Tutorial 144
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
-- Cache values
// Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ...
CMAKE_BUILD_TYPE:STRING=

// Executable file format
CMAKE_EXECUTABLE_FORMAT:STRING=MACHO

// Install path prefix, prepended onto install directories.
CMAKE_INSTALL_PREFIX:PATH=/Applications/Homebrew

// Build architectures for OSX
CMAKE_OSX_ARCHITECTURES:STRING=

// Minimum OS X version to target for deployment (at runtime); newer APIs weak linked. Set to empty string for default value.
CMAKE_OSX_DEPLOYMENT_TARGET:STRING=

// The product will be built against the headers and libraries located inside the indicated SDK.
CMAKE_OSX_SYSROOT:STRING=

// Use tutorial provided math implementation
USE_MYMATH:BOOL=ON

Scanning dependencies of target MathFunctions
[ 25%] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.o
[ 50%] Linking CXX static library libMathFunctions.a
[ 50%] Built target MathFunctions
Scanning dependencies of target Tutorial
[ 75%] Building CXX object CMakeFiles/Tutorial.dir/main.cpp.o
[100%] Linking CXX executable Tutorial
[100%] Built target Tutorial
./Tutorial ver. 1.0
mysqrt
The square root of 144 is 12
```

```bash
|--CMakeCache.txt
|--CMakeFiles
|  |--3.14.5
|  |  |--CMakeCCompiler.cmake
|  |  |--CMakeCXXCompiler.cmake
|  |  |--CMakeDetermineCompilerABI_C.bin
|  |  |--CMakeDetermineCompilerABI_CXX.bin
|  |  |--CMakeSystem.cmake
|  |  |--CompilerIdC
|  |  |  |--CMakeCCompilerId.c
|  |  |  |--a.out
|  |  |  |--tmp
|  |  |--CompilerIdCXX
|  |  |  |--CMakeCXXCompilerId.cpp
|  |  |  |--a.out
|  |  |  |--tmp
|  |--CMakeDirectoryInformation.cmake
|  |--CMakeOutput.log
|  |--CMakeTmp
|  |--Makefile.cmake
|  |--Makefile2
|  |--TargetDirectories.txt
|  |--Tutorial.dir
|  |  |--CXX.includecache
|  |  |--DependInfo.cmake
|  |  |--build.make
|  |  |--cmake_clean.cmake
|  |  |--depend.internal
|  |  |--depend.make
|  |  |--flags.make
|  |  |--link.txt
|  |  |--main.cpp.o
|  |  |--progress.make
|  |--cmake.check_cache
|  |--feature_tests.bin
|  |--feature_tests.c
|  |--feature_tests.cxx
|  |--progress.marks
|--Makefile
|--MathFunctions
|  |--CMakeFiles
|  |  |--CMakeDirectoryInformation.cmake
|  |  |--MathFunctions.dir
|  |  |  |--CXX.includecache
|  |  |  |--DependInfo.cmake
|  |  |  |--build.make
|  |  |  |--cmake_clean.cmake
|  |  |  |--cmake_clean_target.cmake
|  |  |  |--depend.internal
|  |  |  |--depend.make
|  |  |  |--flags.make
|  |  |  |--link.txt
|  |  |  |--mysqrt.cpp.o
|  |  |  |--progress.make
|  |  |--progress.marks
|  |--Makefile
|  |--cmake_install.cmake
|  |--libMathFunctions.a
|--Tutorial
|--TutorialConfig.h
|--cmake_install.cmake
```

有効時の時にライブラリのビルドが入るので、出力されているファイルが増えているのがわかる。
ひとまずビルドが通るところまでいければ今回の目的は達成なので、ひとまずここまで。
