---
layout : post
title  : 特定のメンバ関数だけ特殊化
date   : 2016/09/13
lastchange : 2017-03-06 00:09:51.
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
  X,
  Y
};


template<Status S, Version V>
struct Class{


  void Fx(void){ std :: cout << “Fx” << std :: endl; }
  void Gx(void){ std :: cout << “Gx” << std :: endl; }
};


template<>
void Class<A, X> :: Gx(void){ std :: cout << “Gx: A, X” << std :: endl; };

int main(void){

  Class<A, X> c;

  c.Fx();
  c.Gx();

  Class<B, X> d;
  d.Fx();
  d.Gx();

  return 0;
}
```

## output

```cpp
Fx
Gx: A, X
Fx
Gx
```

なるほろ！

