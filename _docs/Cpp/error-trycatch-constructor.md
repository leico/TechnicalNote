---
layout : post
title  : "エラー処理: コンストラクタとtry-catch"
date   : 2019/08/13
lastchange : 2019-08-14 06:28:58.
tags   :
  - C++
  - C++11
  - Error
  - try
  - catch
---

## 動作順序が複雑なのだ

C++ が複雑になっている一因に動作順序や各要素間の関係が厳格で融通が利かないことがあると思う。
その結果いろいろな落とし穴が発生する、というのはこの例外処理も同じ。




## コンストラクタからの `throw`



```cpp
#include <iostream>
#include <stdexcept> 

class A {
  public:
   A( void ){ std :: cout << "A constructor" << std :: endl; };
  ~A( void ){ std :: cout << "A destructor"  << std :: endl; };
};

class B {
  public:
   B( void ){ std :: cout << "B constructor" << std :: endl; };
  ~B( void ){ std :: cout << "B destructor"  << std :: endl; };
};


class C { 

  A _a;
  B _b;

  public:
  C( void ) : 
      _a()
    , _b()
  { 
    throw std :: runtime_error( "C constructor error" );
  }

  ~C( void ){ std :: cout << "C destructor" << std :: endl; }
};




int main( void ) {

  try{ 
    C c;
  }
  catch( std :: exception& e ){ 
    std :: cout << e.what() << std :: endl;
  }


  return 0;
}
```

`C` は内部に `A` と `B` を持つクラス。このコードは問題なくエラーのタイミングで `A` 、 `B` 
のデストラクタが呼ばれる。


```
A constructor
B constructor
B destructor
A destructor
C constructor error
Program ended with exit code: 0
```

では、`A` 、 `B` ともにポインタだった場合はどうなるだろうか。

```cpp
#include <iostream>
#include <stdexcept> 

class PA {
  public:
   PA( void ){ std :: cout << "PA constructor" << std :: endl; };
  ~PA( void ){ std :: cout << "PA destructor"  << std :: endl; };
};

class PB {
  public:
   PB( void ){ std :: cout << "PB constructor" << std :: endl; };
  ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C { 

  PA* _pa;
  PB* _pb;

  public:
  C( void ) : 
      _pa( new PA() )
    , _pb( new PB() )
  { 
    throw std :: runtime_error( "C constructor error" );
  }
  ~C( void ){ std :: cout << "C destructor" << std :: endl; }
};




int main( void ) {

  try{ 
    C c;
  }
  catch( std :: exception& e ){ 
    std :: cout << e.what() << std :: endl;
  }


  return 0;
}
```


```cpp
  PA* _pa;
  PB* _pb;

  public:
  C( void ) : 
      _pa( new PA() )
    , _pb( new PB() )
  { 
    throw std :: runtime_error( "C constructor error" );
  }
```

`C` が保持するメンバ変数がポインタ型へ、及びコンストラクタの初期化子が変化している。
これで実行すると

```cpp
PA constructor
PB constructor
C constructor error
Program ended with exit code: 0
```

残念ながらメモリリークが発生。 `PA`、 `PB` のデストラクタが呼ばれていない。

解決方法の内1つはちゃんと`delete` する。

```cpp
#include <iostream>
#include <stdexcept> 

class PA {
  public:
   PA( void ){ std :: cout << "PA constructor" << std :: endl; };
  ~PA( void ){ std :: cout << "PA destructor"  << std :: endl; };
};

class PB {
  public:
   PB( void ){ std :: cout << "PB constructor" << std :: endl; };
  ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C { 

  PA* _pa;
  PB* _pb;

  public:
  C( void ) : 
      _pa( new PA() )
    , _pb( new PB() )
  { 
    delete _pa;
    delete _pb;
    throw std :: runtime_error( "C constructor error" );
  }
  ~C( void ){ std :: cout << "C destructor" << std :: endl; }
};




int main( void ) {

  try{ 
    C c;
  }
  catch( std :: exception& e ){ 
    std :: cout << e.what() << std :: endl;
  }


  return 0;
}

```

```cpp
  C( void ) : 
      _pa( new PA() )
    , _pb( new PB() )
  { 
    delete _pa;
    delete _pb;
    throw std :: runtime_error( "C constructor error" );
  }
```

`throw` 前に `delete` しているところが変更点。実行結果は以下


```
PA constructor
PB constructor
PA destructor
PB destructor
C constructor error
Program ended with exit code: 0
```

ちゃんとデストラクタが呼ばれた。

もう1つは、スマートポインタで保持する方法。

```cpp
#include <iostream>
#include <stdexcept> 

class PA {
  public:
   PA( void ){ std :: cout << "PA constructor" << std :: endl; };
  ~PA( void ){ std :: cout << "PA destructor"  << std :: endl; };
};

class PB {
  public:
   PB( void ){ std :: cout << "PB constructor" << std :: endl; };
  ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C { 

  std :: unique_ptr< PA > _pa;
  std :: unique_ptr< PB > _pb;

  public:
  C( void ) : 
      _pa( new PA() )
    , _pb( new PB() )
  { 
    throw std :: runtime_error( "C constructor error" );
  }
  ~C( void ){ std :: cout << "C destructor" << std :: endl; }
};




int main( void ) {

  try{ 
    C c;
  }
  catch( std :: exception& e ){ 
    std :: cout << e.what() << std :: endl;
  }


  return 0;
}
```

```cpp
class C { 

  std :: unique_ptr< PA > _pa;
  std :: unique_ptr< PB > _pb;

  public:
  C( void ) : 
```

保持するメンバ変数を `std :: unique_ptr` にしているところが変更点。実行結果は以下

```
PA constructor
PB constructor
PB destructor
PA destructor
C constructor error
Program ended with exit code: 0
```

こちらもちゃんとデストラクタが呼ばれた。


## メンバ変数にポインタを持っている場合の注意点

* `throw` 前にかならず `delete` すること
* コンストラクタでも忘れないこと
* あるいは考えなくていい方法、例えばスマートポインタなどを活用すること
