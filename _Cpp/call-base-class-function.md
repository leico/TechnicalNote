---
layout : post
title  : 基底クラスのメンバ関数を呼び出したいんだけど・・・
date   : 2016/09/13
lastchange : 2017-03-05 23:59:01.
tags   :
  - C++
  - inheritance
  - instance
  - pure
  - virtual
  - virtual function table
---

## class.h

```cpp
class C{
  public:
             C(void);
    virtual ~C(void);

    virtual void F(void) = 0;
    virtual void G(void) = 0;
    virtual void H(void) = 0;
};

class D : public C{

  public:
             D(void);
    virtual ~D(void);

    virtual void F(void);
    virtual void G(void);
    virtual void H(void);
};

class E : public D{

  public:
             E(void);
    virtual ~E(void);

    virtual void F(void);
    virtual void G(void);
    virtual void H(void);

};
```

## class.cpp

```cpp
#include <iostream>
using namespace std;

C ::  C(void){}
C :: ~C(void){}

D ::  D(void) : C(){}
D :: ~D(void)      {}

D :: F(void){ cout << "D :: F" << endl; }
D :: G(void){ F(); }
D :: H(void){ G(); }

E ::  E(void) : D(){}
E :: ~E(void)      {}
E :: F(void){ D :: F(); cout << "E :: F" << endl; }
E :: G(void){ D :: G(); }
E :: H(void){ D :: H(); }
```

## main.cpp

```cpp
#include <iostream>
using namespace std;

int main(void){
  C *c = new E();

  c -> F();
  c -> G();
  c -> H();

  delete c;
  return 0;
}
```

これが、

## output

```
D :: F
E :: F
D :: F
E :: F
D :: F
E :: F
```

こうなる。

```
E :: F -> D :: F
E :: G -> D :: G -> E :: F -> D :: F
E :: H -> D :: H -> E :: G -> D :: G -> E :: F -> D :: F
```

と呼び出されているようだ。つまり、
`D :: G()`で呼び出している`F()`は`E :: F()`になっていて、 `D :: H()`で呼び出している`G()`は`E :: G()`になっている。

仮想関数テーブルってむずかし。

```
D :: F(void){ cout << "D :: F" << endl; }
D :: G(void){ D :: F(); }
D :: H(void){ D :: G(); }
```

こうするといい感じ。

（　´ﾟдﾟ｀）えーーー

