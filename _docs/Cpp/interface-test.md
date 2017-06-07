---
layout : post
title  : インターフェイスの実験
date   : 2016/09/13
lastchange : 2017-03-05 23:38:24.
tags   :
  - C++
  - interface
  - pure
  - virtual
---
## main.cpp 
```Cpp
#include <iostream>

class Interface{
  public:

    virtual ~Interface(){};

            void Test (){ std :: cout << “Test” << std :: endl; };
    virtual void Test2() = 0;
};

class Impl : public Interface{

  public:
  Impl(){
    Test ();
    Test2();
  }

  void Test2(){ std :: cout << “Come” << std :: endl; };

};



int main(int argc, const char * argv[])
{

  Impl impl;

    return 0;
}
```

```
Test
Come
```

C++のインターフェイスについて。

純粋仮想関数を持つクラスの継承先から継承元の実装を呼べるかテスト。
