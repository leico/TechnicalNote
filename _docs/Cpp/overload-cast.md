---
layout : post
title  : キャスト演算子のオーバーロード
date   : 2016/09/13
lastchange : 2017-03-06 01:24:51.
tags   :
  - C++
  - cast
  - class
  - member
  - operator
  - overload
  - template
---

クラスに独自のキャスト演算子を実装すると、クラスのインスタンスから実装した型への変換が必要になった際、
自動的にキャストを行うようになる。キャスト演算子のオーバーロードは以下のように記述する。

```cpp
operator TYPE() const{}
```

以下の例はCのクラスからint型へのキャスト。 外部から利用するデータが一種類だけの場合、
そのデータに対するキャストを実装しておくとまるで変数のように扱えて便利。

```cpp
include <iostream>


class C{

    int value;

    public:
    C(int v) : value(v) {}
    operator const int & (){ return value; }

};

int main(int argc, const char * argv[]) {

    C c(100);

    std :: cout << c << std :: endl; //100

    return 0;

}
```

テンプレートクラスで実装するとこうなる。

```cpp
template<typename TYPE>

class C{

    TYPE value;

    public:
    C(TYPE v) : value(v){};
    operator const TYPE& (void){ return value; }

};
```
