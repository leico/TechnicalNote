---
layout : post
title  : 可変引数テンプレートパラメータいろいろ
date   : 2017/08/02
lastchange : 2018-08-02 19:10:42.
tags   :
  - C++
  - C++11
  - variadic template
  - parameter pack
  - テンプレートメタ
---

## 可変引数テンプレートパラメータとテンプレートメタ

について、気になったことついでに少し簡単にまとめる。




## 可変引数テンプレートの基礎


### main.cpp と解説
```cpp

#include <iostream>

template < typename... Args >
void F( Args... args ){

  std :: cout << "call" << std :: endl;
}

int main(int argc, const char * argv[]) {

  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();
  

  
    return 0;
}
```

このソースコードの

```cpp
template < typename... Args >
```

この部分が可変引数テンプレート。
複数の、任意の型が必要なテンプレート関数、テンプレートクラスを簡単に定義できるようになった。

```cpp
template < typename... Args >
void F( Args... args ){

  std :: cout << "call" << std :: endl;
}
```

任意の、複数の型を受け取る関数を定義している。
任意の、複数の型というのを関数の引数部分 `Args... args` で展開している。


```cpp
  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();
```

どのような引数がどれだけきても、逆に引数が全く無くてもそれに沿った関数が自動生成される。
可変引数は \\(0 \to n \\) の任意の数の型を受け取る。

### 出力結果

```cpp 
call
call
call
call
call
```




## 引数の数で特殊化する

### main.cppと解説

```cpp
#include <iostream>

template < typename... Args >
void F( Args... args ){

  std :: cout << "non args" << std :: endl;
}

template < typename Head, typename... Tail >
void F ( Head head, Tail... tail ) {

  std :: cout << "more than 1 args" << std :: endl;
}



int main(int argc, const char * argv[]) {

  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();

  return 0;
}
```

```cpp
template < typename Head, typename... Tail >
void F ( Head head, Tail... tail ) {

  std :: cout << "more than 1 args" << std :: endl;
}
```

この関数は1つ以上の引数を受け取る場合に呼び出される。
2つ以上のパラメータを受け取るように見えるが、`Tail = void` もあり得る。
であるならば必然的に 1個以上の引数が指定された場合、この関数に合致するのがわかる。
逆に、引数が何もない = `void` 場合、 `Head = void` 、 `Tail = ???` となるので
引数が何もない呼び出しでこの関数が呼び出されることはない。

オーバーロードされた関数の探索時の優先順位は、
合致する特殊化、専門化、特化したものが優先的に呼び出される。
逆に汎用性のあるものは優先順位が低い。
特殊化、専門化、特化されたものから順番に、条件に合う関数を探索するようになっている。

なので

```cpp
template < typename... Args >
void F( Args... args ){

  std :: cout << "non args" << std :: endl;
}
```

が最も汎用性があるので呼び出しの優先順位が一番低い。

ゆえに

```cpp
//引数が0個の場合
template < typename... Args >
void F( Args... args ){

  std :: cout << "non args" << std :: endl;
}

//引数が1個以上の場合の特殊化
template < typename Head, typename... Tail >
void F ( Head head, Tail... tail ) {

  std :: cout << "more than 1 args" << std :: endl;
}
```

と考えることができる。

```cpp
  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();
```

この場合、上4つが1つ以上の引数に特化した `F(Head, ...)` が、
最後だけ `F(...)` が呼ばれるはずである。



### 出力結果

```cpp
more than 1 args
more than 1 args
more than 1 args
more than 1 args
non args
```

## 可変引数の個数を取得する

### main.cppと解説

```cpp
#include <iostream>

template < typename... Args >
void F( Args... args ){

  std :: cout << sizeof...( Args ) << std :: endl;
}


int main(int argc, const char * argv[]) {

  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();

  return 0;
}
```

`sizeof...( [parameter pack] )` で可変引数(パラメータパック) の総数を取得することができる。

### 出力結果

```cpp
1
1
1
3
0
```


## 関数の呼び出し実験

### main.cppと解説

```cpp
#include <iostream>

template < typename T > 
void print ( T& t ) {
  std :: cout << "T:" << typeid( T ).name() << std :: endl;
}



template < typename Head, typename... Tail > 
void print( Head head, Tail... tail){
  std :: cout << "Head & Tail: " << typeid( Head ).name() << ",";
  print( tail... );
}



void print ( void ){
  std :: cout << "void" << std :: endl;
}



template < typename... Args >
void F( Args... args ){

  print(args...);
}


int main(int argc, const char * argv[]) {

  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();

  return 0;
}
```

```cpp
template < typename... Args >
void F( Args... args ){

  print(args...);
}
```

受け取った可変引数を `print(...)` 関数に渡している。
`print(...)` 関数はこのように3つ用意されている。


```cpp
//引数1つだけ
template < typename T > 
void print ( T& t );

//引数1つ以上の場合
template < typename Head, typename... Tail > 
void print( Head head, Tail... tail);

//引数なし
void print ( void );
```

引数が1つ以上の場合、受け取った引数の先頭の型を出力するようになっている。

```cpp
  //1つだけの時
  std :: cout << "T:" << typeid( T ).name() << std :: endl;

  //1つ以上の時
  std :: cout << "Head & Tail: " << typeid( Head ).name() << ",";

```

1つ以上の引数がある場合に、全ての引数を出力するために先頭以外の引数をもう一度 `print(...)`
に譲渡し、再びいずれかの `print(...)` が呼ばれるようにしている。

```cpp
  print( tail... );
```

各々の出力に際し、どの関数が呼ばれたのかわかるように接頭辞を付けている。

```cpp

  //1つだけの時
  std :: cout << "T:" << ...

  //1つ以上の時
  std :: cout << "Head & Tail: " << ...

  //引数なし
  std :: cout << "void" << ...

```


```cpp
  F("arg");
  F( 10 );
  F( 0.1 );
  F( "arg", 10, 0.1 );
  F();
```

上3つは引数が1つのものが呼ばれるだろうが、4つ目はどのように展開されるか、
引数がない場合はどれが呼ばれるのか確かめる。

### 出力結果

```cpp
T:PKc
T:i
T:d
Head & Tail: PKc,Head & Tail: i,T:d
void
```


## 再帰による展開とテンプレートメタ

テンプレートメタプログラミングでは

```cpp
using type                  = std :: true_type;
static constexpr bool value = true;
```

といったクラス内の変数や型を返り値として利用する。

`using ...` の場合、新たに型の別名を定義しているだけであり、
`static constexpr ...` の場合クラスがいくつ増えても1つしか実体化しない、
コンパイル時のみ存在する、といったことがあり得るのでメモリを逼迫することがない。

それどころかそもそもクラス自体をインスタンス化させなかったりする。

関数の場合も同様で、関数の定義と返り値の型のみを利用し、
関数本体を展開しないどころか、実装が存在しないということもある。

プリプロセッサとソースコードのコンパイルの間に
もう一段階前処理を挟む感じになる。


これらの型を利用してコンパイル時にテンプレート引数の型を操作することで、
コンパイル時に可能な処理を全て終えてしまう。
なぜそのようなことができるのかを簡単にまとめると、

1. 関数の再帰によるループ処理
2. オーバーロード、部分特殊化により条件分岐

ができるからである。

その簡単な例題として、入力された複数の型の内、いずれかが

```cpp
struct Container {
  static constexpr bool value = true;
};
```

となっていたら 

```cpp
using type = std :: true_type;
```

を定義するメタプログラミングを実装してみる。

ここで条件としている

```cpp
struct Container {
  static constexpr bool value = true;
};
```

という型は `std :: true_type` として STL で用意されているので、
これを継承したクラスを利用する。


### main.cppと解説


```cpp
#include <iostream>
#include <type_traits> 

template < typename... Args > 
auto Or_impl ( Args... args ) -> std :: false_type ;

template < typename Head, typename... Tail > 
auto Or_impl ( Head head, Tail... tail ) -> typename std :: conditional<
    Head :: value
  , std :: true_type
  , decltype( Or_impl( tail... ))
> :: type ;

template < typename... Args > 
auto Or( Args... args ) -> decltype(
    Or_impl( args... )
) ;


struct A  : std :: true_type  {};
struct B  : std :: false_type {};
struct C  : std :: true_type  {};

int main(int argc, const char * argv[]) {

  std :: cout << decltype( Or( A() ) )                :: value << std :: endl;
  std :: cout << decltype( Or( B() ) )                :: value << std :: endl;
  std :: cout << decltype( Or( B(), B(), C() ) )      :: value << std :: endl;
  std :: cout << decltype( Or( B(), B(), B(), B() ) ) :: value << std :: endl;
  


  return 0;
}
```

```cpp
  ...
  std :: cout << decltype( Or( B(), B(), C() ) ) :: value << std :: endl;
  ... 
```

今回の実装では関数 `Or(...)` に渡す引数の型によって、返り値の型が変化する。
`decltype( Or(...) )` によって、返り値の型のみを取得している。

渡している引数はそれぞれ

```cpp
struct A  : std :: true_type  {};
struct B  : std :: false_type {};
struct C  : std :: true_type  {};
```

と定義されている。
`B` のみ、 `std :: false_type` が継承されているため、
`B :: value = false` となる。

プログラムの処理工程は

1. 先頭の`Container :: value` が `true` だった場合そこで `std :: true_type` を返す 
2. `false` だった場合、先頭を除いた可変引数で 1. の判定を行う
3. 可変引数の終端、何もない状態までいってしまったら `std :: false_type` を返す

というなんてことないものになる。これを実装している関数が以下の3つ。

```cpp
//実装部分:引数が0個の時
template < typename... Args > 
auto Or_impl ( Args... args );

//実装部分:引数が1個以上の時
template < typename Head, typename... Tail > 
auto Or_impl ( Head head, Tail... tail );

//ユーザが利用するインターフェイス
template < typename... Args > 
auto Or( Args... args );
```

一つずつ内容を確認してゆく。

```cpp
template < typename... Args > 
auto Or( Args... args ) -> decltype(
    Or_impl( args... )
) ;
```

`Or(...)` がユーザが利用するインターフェイスになる。
後置で返り値を設定している部分で、

```cpp
-> decltype( Or_impl( args... ) ) ;
```

と `Or_impl( ... )` 関数の返り値の型を、自身の返り値の型に指定している。
`Or_impl(...)`へ引数に、自身の引数を譲渡している。

本来であれば、返り値の後部に関数の処理が存在するが、
返り値の型だけが重要、かつ返り値の部分で処理が完結しなければならないので
関数のプロトタイプ宣言のみになっている。関数を実行時に呼び出そうとすればエラーが出るが、
呼び出しを行わないので問題がない。

```cpp
template < typename... Args > 
auto Or_impl ( Args... args ) -> std :: false_type ;
```

引数が0になった場合の処理になる。

```cpp
-> std :: false_type
```

と `std :: false_type` を定義している。ここまでは簡単だが、

```cpp
template < typename Head, typename... Tail > 
auto Or_impl ( Head head, Tail... tail ) -> typename std :: conditional<
    Head :: value
  , std :: true_type
  , decltype( Or_impl( tail... ))
> :: type ;
```

1個以上の引数が存在する時の処理。ここで分岐とループ処理を行っている。

```cpp
-> typename std :: conditional<
    /* 条件 */
  , /*true 時の型*/
  , /* false 時の型*/
> :: type /* 条件により決定された型 */
```

`std :: conditional` は 条件部分が `true` / `false` で内部の `type` が指す型が変化するテンプレートメタ関数。
`true` 時の型は第2引数、`false`時の型は第3引数で指定する。

条件部分には

```cpp
Head :: value
```

つまり、 `Head :: value` が `true` / `false` で型が変わる。`true` の場合、

```cpp
std :: true_type
```

みての通り `std :: true_type` 型が指定されループが終了する。 `false` の場合、

```cpp
decltype( Or_impl( tail... ))
```

先頭を除いた可変引数を用いて `Op_impl(...)` のいずれかを呼び出している。
ここで呼び出された `Op_impl(...)` のどこかで `true` が発生すれば、`Op_impl(...)` の返り値の型は
`std :: true_type` になり、空になるまで探索が行われれば `std :: false_type` が返り値の型になる。

```cpp
  std :: cout << decltype( Or( A() ) )                :: value << std :: endl;
  std :: cout << decltype( Or( B() ) )                :: value << std :: endl;
  std :: cout << decltype( Or( B(), B(), C() ) )      :: value << std :: endl;
  std :: cout << decltype( Or( B(), B(), B(), B() ) ) :: value << std :: endl;
```

以上のことから型のOR演算が実装される。`Container :: value == true` が存在する時のみ、
`decltype(Or(...) ) :: value` が `true` になる。


### 実行結果

```cpp
1
0
1
0
```


## Or をクラスで実装する

### main.cppと解説

```cpp
#include <iostream>
#include <type_traits> 

template < typename... Args > 
struct Or_impl 
{ using type = std :: false_type ; };

template < typename Head, typename... Tail >
struct Or_impl <Head, Tail...> {
  using type = typename std :: conditional<
      Head :: value
    , std :: true_type
    , typename Or_impl< Tail... > :: type 
  > :: type;
};

template < typename... Args > 
struct Or {
  using type = typename Or_impl< Args... > :: type;
};


struct A  : std :: true_type  {};
struct B  : std :: false_type {};
struct C  : std :: true_type  {};

int main(int argc, const char * argv[]) {

  std :: cout << Or< A >          :: type :: value << std :: endl;
  std :: cout << Or< B >          :: type :: value << std :: endl;
  std :: cout << Or< B, B, C >    :: type :: value << std :: endl;
  std :: cout << Or< B, B, B, B > :: type :: value << std :: endl;
  


  return 0;
}
```

上記 Or をクラスで実装したもの。

```cpp
//実装部分:引数が0個の時
template < typename... Args > 
struct Or_impl;

//実装部分:引数が1個以上の時
template < typename Head, typename... Tail >
struct Or_impl <Head, Tail...>;

//ユーザが利用するインターフェイス
template < typename... Args > 
struct Or;
```

関数の同じ名前のものがそれぞれに対応している。
返り値となる型はクラス内の

```cpp
using type = ...;
```

と、`Container :: type` に型を指定することで実装している。

```cpp
  std :: cout << Or< A >          :: type :: value << std :: endl;
  std :: cout << Or< B >          :: type :: value << std :: endl;
  std :: cout << Or< B, B, C >    :: type :: value << std :: endl;
  std :: cout << Or< B, B, B, B > :: type :: value << std :: endl;
```

呼び出し方法が関数からクラスになっているので変化している。
`... :: type` は public な型であるし、 `... :: value` も `static constexpr` な変数なので、
それぞれクラスをインスタンス化することなく利用することができる。

### 出力結果

```cpp
1
0
1
0
```

`... :: type :: value` と少し変わった利用方法になるが、クラスの方がいろいろと優遇される気がする。
例えば




## 実装をカプセル化する

### main.cppと解説

```cpp
#include <iostream>
#include <type_traits> 


template < typename... Args > 
struct Or {

private:

  template < typename... InArgs > 
    struct Or_impl { using type = std :: false_type ; };

  template < typename Head, typename... Tail >
    struct Or_impl <Head, Tail...> {
      using type = typename std :: conditional<
          Head :: value
        , std :: true_type
        , typename Or_impl< Tail... > :: type 
        > :: type;
    };


public:
  using type = typename Or_impl< Args... > :: type;
};


struct A  : std :: true_type  {};
struct B  : std :: false_type {};
struct C  : std :: true_type  {};

int main(int argc, const char * argv[]) {

  std :: cout << Or< A >          :: type :: value << std :: endl;
  std :: cout << Or< B >          :: type :: value << std :: endl;
  std :: cout << Or< B, B, C >    :: type :: value << std :: endl;
  std :: cout << Or< B, B, B, B > :: type :: value << std :: endl;
  


  return 0;
}
```

`Or_impl(...)` など実装が外部に出ると名前空間が汚れるので、`private` 内に実装を作ってカプセル化した。
こういうことはクラスでないとできない。

```cpp
template < typename... Args > 
struct Or {

private:

  template < typename... InArgs > 
    struct Or_impl { using type = std :: false_type ; };

  template < typename Head, typename... Tail >
    struct Or_impl <Head, Tail...> {
      using type = typename std :: conditional<
          Head :: value
        , std :: true_type
        , typename Or_impl< Tail... > :: type 
        > :: type;
    };


public:
  using type = typename Or_impl< Args... > :: type;
};
```

クラスの実装。 型返り値は

```cpp
public:
  using type = typename Or_impl< Args... > :: type;
```

となっており特に変化しないが、

```cpp
private:
  template < typename... InArgs > 
    struct Or_impl;

  template < typename Head, typename... Tail >
    struct Or_impl <Head, Tail...>;
```

それらは全て `private` 内に実装されている。あとはほとんど同じだが、実装を他者が流用できなくなった。

### 出力結果

```cpp
1
0
1
0
```

関数テンプレートに勝る点として他に、テンプレート可変引数を関数の可変引数に展開せずに利用できる、
といったこともある。

```cpp
template < typename... Args > 
using Temp = Or< Args... >;
```

関数テンプレートに引数がある場合、こういう記述が難しい。
