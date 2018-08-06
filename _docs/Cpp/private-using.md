---
layout : post
title  : using や typedef にもアクセス指定子が乗る
date   : 2018/08/05
lastchange : 2018-08-05 22:24:12.
tags   :
  - C++
  - C++11
  - アクセス指定子
  - access specifiers
  - using
  - typedef
---

## 当たり前といえば当たり前かもしれないけれど

特に検索しても出てこないのでやってみた。当然ながら利用できた。

## main.cpp

```cpp
struct container { 

private: 
  using private_type = int;

public:
  using type = int;

};



int main(int argc, const char * argv[]) {

  container :: type t       = 0;
  container :: private_type = 0;


  return 0;
}
```

## 結果

```cpp
container :: private_type = 0;
```

の部分で

```
'private_type' is a private member of 'container'
```

とおこられた。




## 応用例

using にメタプログラミングなクラスを指定してあげると簡単な関数が実装できるので、
ちょっとした内部処理を記述するのに向いている。

以下の例はポインタでない処理したい時

```cpp
template < typename T > 
class container {

  private: 
    using non_pointer_type = std :: remove_pointer< T > :: type;

    template < typename Value > 
    struct non_pointer_process { 
      using type  ....
    };

  public: 
    using type = non_pointer_process< non_pointer_type< T > > :: type;

};
```
