---
layout : post
title  : インターフェイスを継承して新しいインターフェイスを作る
date   : 2016/09/13
lastchange : 2017-03-06 00:03:14.
tags   :
  - C++
  - inheritance
  - interface
  - pure
  - virtual
---

## main.cpp

```cpp
class C{

  public:
  virtual ~C(void){};

  virtual void Fx(void) = 0;
  virtual void Gx(void) = 0;

};

class D : public C{

  public:
           D(void) : _num(0){};
  virtual ~D(void)          {};

  protected:
  int _num;

};

class E : public D{

  public:
           E(void) : D(){};
  virtual ~E(void)      {};

  virtual void Fx(void){ std :: cout << _num << std :: endl; }
  virtual void Gx(void){ std :: cout << "Gx" << std :: endl; }

};

int main(void){
    C *c = new E();
    c -> Fx();
    c -> Gx();

    delete c;

    D *d = new E();
    d -> Fx();
    d -> Gx();

    delete d;

    return 0;
}
```

## output

```
0
Gx
0
Gx
```

できるもんだなぁ。ただし、`C *c = new D();`
は純粋仮想関数を実装してないから怒られる。
