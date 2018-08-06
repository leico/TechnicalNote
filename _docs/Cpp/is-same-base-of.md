---
layout : post
title  : "std :: is_same と std :: is_base_of の挙動を調べる"
date   : 2018/08/05
lastchange : 2018-08-05 22:35:25.
tags   :
  - C++
  - C++11
  - is_base_of
  - is_same
---

## A と同じ型、または A を基底とする型を調べたかったので

`std :: is_same` と `std :: is_base_of` の挙動を調べた

## main.cpp

```cpp
#include <iostream> 
#include <type_traits> 

struct Base {}; 
struct A : public Base {};



int main(int argc, const char * argv[]) {

  std :: cout << std :: is_same   < Base, A >    :: value << std :: endl;
  std :: cout << std :: is_base_of< Base, A >    :: value << std :: endl;
  std :: cout << std :: is_base_of< A, Base >    :: value << std :: endl;
  std :: cout << std :: is_base_of< Base, Base > :: value << std :: endl;
  std :: cout << std :: is_base_of< A, A >       :: value << std :: endl;

  return 0;
}

```

## 出力結果

```
0
1
0
1
1
```

`is_same< [基底], [派生]> :: value` 
: 別の型であると認識される( `false` )

`is_base_of< [基底], [派生] > :: value`
: [基底] からの [派生] クラスとして検出される( `true` )

`is_base_of< [派生], [基底] > :: value`
: [基底] が [派生] の派生クラスではない( `false` )

`is_base_of< [基底], [基底] > :: value`
: 型が同一の場合、同じ基底と認識される( `true` )

`is_base_of< [派生], [派生] > :: value`
: 型が同一の場合、同じ基底と認識される( `true` )


## A と同じ型、または A を基底とする型を調べるには

```cpp
template < typename Type > 
using base_of_A = std :: is_base_of<A, Type> :: value;
```
