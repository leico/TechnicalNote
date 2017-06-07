---
layout : post
title  : テンプレートメタの入り口
date   : 2016/09/13
lastchange : 2017-03-06 00:07:07.
tags   :
  - C++
  - genelic
  - programming
  - template
  - meta
---


## main.cpp

```cpp
#include <iostream>

  enum Status{
    A,
    B
  };

  enum Version{
    C,
    D
  };

template <Status S, Version V>
struct Data{

  static void Fx(void){}
};

template <> struct Data<A, C>{ static void Fx(void){ std :: cout << “A : C” << std :: endl; } };
template <> struct Data<A, D>{ static void Fx(void){ std :: cout << “A : D” << std :: endl; } };
template <> struct Data<B, C>{ static void Fx(void){ std :: cout << “B : C” << std :: endl; } };
template <> struct Data<B, D>{ static void Fx(void){ std :: cout << “B : D” << std :: endl; } };

int main(void){

  Data<A, C> :: Fx();
  Data<B, C> :: Fx();
  Data<A, D> :: Fx();
  Data<B, D> :: Fx();

  return 0;
}
```

## output

```
A : C
B : C
A : D
B : D
```
まぁそういう感じよね。

