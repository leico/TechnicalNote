---
layout : post
title  : Explicit specialization of “ after instantiation
date   : 2016/09/13
lastchange : 2017-03-06 00:13:17.
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

template<typename T>
class C{

  public:
   C(void){}
  ~C(void){}

  void Fx(void);
  void Gx(void);
};

template<>
inline void C<int> :: Fx(void){
  std :: cout << “Fx” << std :: endl;
  Gx();
}

template<>
inline void C<int> :: Gx(void){
  std :: cout << “Gx” << std :: endl;
}

int main(int argc, const char * argv[])
{

  C<int> c;

  c.Fx();

  return 0;
}
```

こんなソースコードで`Explicit specialization of ‘Gx’ after instantiation`って怒られた。

テンプレート特殊化の際には、関数の中で特殊化した関数を呼び出す時は、
先に呼び出す関数を記述してあげないといけない。

## main.cpp

```cpp
#include <iostream>

template<typename T>
class C{

  public:
   C(void){}
  ~C(void){}

  void Fx(void);
  void Gx(void);
};

template<>
inline void C<int> :: Gx(void){
  std :: cout << “Gx” << std :: endl;
}
template<>
inline void C<int> :: Fx(void){
  std :: cout << “Fx” << std :: endl;
  Gx();
}



int main(int argc, const char * argv[])
{

  C<int> c;

  c.Fx();

  return 0;
}
```

これならコンパイルが通る。FxとGxの実装順番を変えただけ。

## output

```
Fx
Gx
```
