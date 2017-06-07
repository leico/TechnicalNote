---
layout : post
title  : アクセサ
date   : 2016/09/13
lastchange : 2017-03-06 01:21:10.
tags   :
  - C++
  - class
  - member
  - overload
  - accessor
---


C++でメンバ変数へのアクセサを作る場合、最近こうやっている。

```cpp
class C{

private:
  int _value;
public:
   C(){};
  ~C(){};
  int Value(void){return _value;}
  int Value(const int value){ _value = value; return _value; }

};
```

オーバーロードを用いてこんな感じにしている。

メンバ変数と同じような名前にすることで

```cpp
int main(void){
  C c;
  c.Value( 1970 + 45 );
  std :: cout << 100 + c.Value() << std :: endl;

  return 0;
}
```

`get*`、`set*`とするよりかなりスマートでは？

テンプレートクラスの場合はこんな感じ。

```cpp
template<typename T>
class C{
  private:
  T _value;
  public:
    C(void){};
   ~C(void){};

   const T& Value(void){ return _value; }
   const T& Value(const T& value){ _value = value; return _value; } 
};

int main(void){

  C<std :: string> c; c.Value(std :: string(“Str :”) );

  std :: cout << c.Value() + “Test String” << std :: endl;

  return 0; 
}
```
