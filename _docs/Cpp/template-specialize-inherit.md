---
layout : post
title  : クラステンプレートの特殊化ごとに継承元を変更する
date   : 2017/08/03
lastchange : 2018-08-03 20:53:50.
tags   :
  - C++
  - C++11
  - template 
  - spacialization
  - テンプレート
  - 特殊化
  - 継承
---

## それってできるの？ と思ったのでやってみたら

できた。特別仕様ごとに継承先を付け替えることができる。

### main.cpp と解説

```cpp
#include <iostream>
#include <type_traits> 


struct X{};


template < typename T > 
struct Base : std :: false_type {};


template <> 
struct Base< X > : std :: true_type {};


int main ( void ){

  std :: cout << Base< int > :: value << std :: endl;
  std :: cout << Base< X >   :: value << std :: endl;


  return 0;
}
```

```cpp
template < typename T > 
struct Base : std :: false_type {};
```

まずは汎用型を定義、こちらは `std :: false_type` を継承している。

```cpp
template <> 
struct Base< X > : std :: true_type {};
```

しかし `X` 型に関しては `std :: true_type` を継承する。

```cpp
std :: cout << Base< int > :: value << std :: endl;
std :: cout << Base< X >   :: value << std :: endl;
```

で、それぞれの `Base<...> :: value` を表示させてみる。

### 出力結果

```cpp
0
1
```
