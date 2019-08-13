---
layout : post
title  : "エラー処理: メンバクラスからの `throw` "
date   : 2019/08/13
lastchange : 2019-08-14 07:24:57.
tags   :
  - C++
  - C++11
  - Error
  - try
  - catch
---


## 続・動作順序が複雑なのだ

それだけでなく文法も複雑が過ぎるのだ。

参考:

* [関数監視ブロック(function-try-block) - エンジニアのソフトウェア的愛情](https://blog.emattsan.org/entry/20080120/1200822228)




## 初期化子のタイミングでの `throw`

この場合はどうなるだろうか。
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
     PB( void ){ throw std :: runtime_error("PB constructor error"); };
    ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C {
  
  PA* _pa;
  PB* _pb;
    
public:
    C( void ) :
      _pa( new PA() )
    , _pb( new PB() )
    { std :: cout << "C constructor " << std :: endl; }

    ~C( void ){
      delete _pa;
      delete _pb;
      std :: cout << "C destructor" << std :: endl; 
    }
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
 PB( void ){ throw std :: runtime_error("PB constructor error"); };
```

`PB` のコンストラクタが例外を投げている。 `C` のコンストラクタ的には
`PA` の `new` が成功してから `PB` のコンストラクタが実行されるが、実行結果

```
PA constructor
PB constructor error
Program ended with exit code: 0
```

やはり `PA` が開放されない。

## 関数監視ブロック

{% capture text %}

# 関数監視ブロック(function-try-block)

こんな構文があるとは知りませんでした。
プログラミング言語C++ (
[アスキーアジソンウェスレイシリーズ―Ascii Addison Wesley programming series](http://d.hatena.ne.jp/asin/475611895X/projematt-22)
)をよく読むと、確かに書いてあります。

```cpp
class Foo
{
public:
    Foo try : bar_()
    {
    }
    catch(...)
    {
        // コンストラクタで例外が発生したよ
        // (初期化リストの例外もcatch!)
        // コンストラクタの場合に限り、自動的に例外が再throwされます
    }

    void foo() try
    {
        // do something
    }
    catch(...)
    {
        // 関数fooで例外が発生したよ
    }

private:
    Bar bar_;
};
```


{% endcapture %}
{% capture source %}
[関数監視ブロック(function-try-block) - エンジニアのソフトウェア的愛情](https://blog.emattsan.org/entry/20080120/1200822228)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


コレを利用しなければならないようだ。

```cpp
class PA {
public:
     PA( void ){ std :: cout << "PA constructor" << std :: endl; };
    ~PA( void ){ std :: cout << "PA destructor"  << std :: endl; };
};

class PB {
public:
     PB( void ){ throw std :: runtime_error("PB constructor error"); };
    ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C {
  
  PA* _pa;
  PB* _pb;
    
public:
    C( void ) try :
      _pa( new PA() )
    , _pb( new PB() )
    { 
      std :: cout << "C constructor " << std :: endl; 
    }
    catch( std :: exception& e ){
      delete _pa;
      //delete _pb; //not allocated error
      std :: cerr << "C constructor error" << std :: endl;
      throw;
    }

    ~C( void ){
      delete _pa;
      delete _pb;
      std :: cout << "C destructor" << std :: endl; 
    }
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
    C( void ) try :
      _pa( new PA() )
    , _pb( new PB() )
    { 
      std :: cout << "C constructor " << std :: endl; 
    }
    catch( std :: exception& e ){
      delete _pa;
      //delete _pb; //not allocated error
      std :: cerr << "C constructor error" << std :: endl;
      throw;
    }
```

利用したとして今度は `PB` が `new` されていないので `delete` できない。
`new` されたかを何かしらで確認しなければならない、が、
ひとまずエラーを捕捉することはできた。実行結果

```
PA constructor
PA destructor
C constructor error
PB constructor error
Program ended with exit code: 0
```

やはりスマートポインタが一番の解決策か。

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
     PB( void ){ throw std :: runtime_error("PB constructor error"); };
    ~PB( void ){ std :: cout << "PB destructor"  << std :: endl; };
};


class C {
 
  std :: unique_ptr< PA > _pa;
  std :: unique_ptr< PB > _pb;
    
public:
    C( void ) try :
      _pa( new PA() )
    , _pb( new PB() )
    { 
      std :: cout << "C constructor " << std :: endl; 
    }
    catch( std :: exception& e ){
      std :: cerr << "C constructor error" << std :: endl;
      throw;
    }

    ~C( void ){
      std :: cout << "C destructor" << std :: endl; 
    }
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
    C( void ) try :
      _pa( new PA() )
    , _pb( new PB() )
    { 
      std :: cout << "C constructor " << std :: endl; 
    }
    catch( std :: exception& e ){
      std :: cerr << "C constructor error" << std :: endl;
      throw;
    }

    ~C( void ){
      std :: cout << "C destructor" << std :: endl; 
    }
};
```

メンバクラスを `std :: unique_ptr` に変更しながら、関数監視ブロックを利用した。
これでデストラクタも問題なく呼ばれ、エラーも捕捉できるはず。実行結果

```
PA constructor
PA destructor
C constructor error
PB constructor error
Program ended with exit code: 0
```

大丈夫だ。スマートポインタは正義。


## メンバクラスのコンストラクタによる `throw` について

* 関数監視ブロック　コンストラクタ用を利用して例外を捕捉できる
* どのメンバクラスまでが実体化しているかを確認する仕組みが必要
  * 確認した上で、実体化している動的クラスの開放をしなければならない
* というのはめんどくさいのでスマートポインタを使おう
  * **スマートポインタは正義**
  * **スマートポインタは人権**


