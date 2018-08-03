---
layout : post
title  : enum class 同士の計算を僕も実装してみた
date   : 2017/08/03
lastchange : 2018-08-04 01:14:26.
tags   :
  - C++
  - C++11
  - enum class
  - 列挙型
  - operator
  - 計算
  - 演算
---

## enumにスコープができたはいいが使いにくい

という問題。名前被り等を気にする必要はなくなったが、
整数型への暗黙の変換も、enum 同士の計算もなにかしらを実装しなければならなくなった。
多くのひとがこれは使いにくいと思っているようで、

* [C++のscoped enumで関数のフラグ指定をしたい - Qiita](https://qiita.com/prickle/items/c4de8cc23556c6a3d93b)
* [君の名は・・・enum class - Qiita](https://qiita.com/yumetodo/items/6f7b5c3714b821fe0196)
* [Re2: C++のscoped enumで関数のフラグ指定をしたい & 君の名は・・・enum class - Qiita](https://qiita.com/akinomyoga/items/a5df34fc78efd86fa4ef)

計算系は偉大な先輩方が強力なものを作ってくれている、が、見習い魔術師には高級言語過ぎて理解できなかったので
自分でも書いてみた。







## main.cppと解説


ソース全文。ひとまず論理和だけ実装しているのだが・・・長い。

```cpp
#include <iostream>
#include <type_traits> 

template < typename T > 
struct enum_cast {

  private:
  static_assert( 
      std :: is_enum< T > :: value
    , "enum_cast only used to enum type" 
  );

  public: 
  using type = T;
  using underlying_type = typename std :: underlying_type< T > :: type ;

  static constexpr underlying_type 
    to_underlying ( const type t ) { return static_cast< underlying_type >( t ) ; }
  
  static constexpr type 
    to_enum ( const underlying_type t ) { return static_cast< type >( t ) ; }
};


template < typename T, typename U  > 
using is_same_underlying = std :: is_same<
    typename enum_cast< T > :: underlying_type 
  , typename enum_cast< U > :: underlying_type 
>;



template < typename... Args> 
class is_enum_logic_safe {

  private:
   
    // no args
    template < typename... InArgs > 
      struct impl : std :: false_type {};

    // single arg
    template < typename Type1, typename... Tail > 
      struct impl< Type1, Tail... > : std :: true_type {};

    // more than 2 args
    template < typename Type1, typename Type2, typename... Tail > 
      struct impl< Type1, Type2, Tail... > : 
      std :: conditional<
          is_same_underlying< Type1, Type2 > :: value 
        , typename impl< Type2, Tail... > :: type 
        , std :: false_type 
      > :: type  
      {};

  public:

    static constexpr bool value = impl< Args... > :: type :: value;
};




template < typename T, typename U = T > 
struct enum_logic_enabled : std :: false_type {}; 


template < typename T, typename U > 
using enum_enabler = std :: enable_if<
    enum_logic_enabled< T, U > :: value &&
    is_enum_logic_safe< T, U > :: type :: value 
  , std :: nullptr_t
>;




template < 
    typename T
  , typename U = T
  , typename enum_enabler< T, U > :: type = nullptr 
> 
static constexpr T operator| ( T t, U u ) {
  return enum_cast< T > :: to_enum( 
    enum_cast< T > :: to_underlying( t ) | enum_cast< U > :: to_underlying( u ) 
  ) ;
}







enum class A : unsigned char { 
  one
};

enum class B : unsigned char {
  two
};

enum class C : unsigned int {
  three
};


template <> 
struct enum_logic_enabled< A > : std :: true_type {};

template <> 
struct enum_logic_enabled< C > : std :: true_type {};

template <> 
struct enum_logic_enabled< A, C > : std :: true_type {};

template <> 
struct enum_logic_enabled< A, B > : std :: true_type {};


int main(int argc, const char * argv[]) {

  A a;
  B b;
  C c;

  a = A :: one   | A :: one;
  //b = B :: two   | B :: two; 
  c = C :: three | C :: three;


  a = A :: one | B :: two ;
  //a = A :: one | C :: three; 
  
  unsigned char uc = 0;
  int i = 0;

  //enum_cast< int > :: type j = 0;


  


  return 0;
}
```




### enum_cast

```cpp
template < typename T > 
struct enum_cast {

  private:
  static_assert( std :: is_enum< T > :: value , "enum_cast only used to enum type" );

  public: 
  using type = T;
  using underlying_type = typename std :: underlying_type< T > :: type ;

  static constexpr underlying_type 
    to_underlying ( const type t ) { return static_cast< underlying_type >( t ) ; }
  
  static constexpr type 
    to_enum ( const underlying_type t ) { return static_cast< type >( t ) ; }
};
```

`enum_cast<T> :: type`
: テンプレート引数で与えられた enum class T

`enum_cast<T> :: underlying_type`
: テンプレート引数で与えられた enum class T の基底型

`enum_cast<T> :: to_underlying(const type t)`
: enum class T を引数にとり、enum class T の基底型を返す

`enum_cast<T> :: to_enum(const underlying_type t)`
: enum class T の基底型を引数にとり、enum class T 型を返す

他に

```cpp
private:
static_assert( std :: is_enum< T > :: value , "enum_cast only used to enum type" );
```

T が enum class でない場合、コンパイル時に `enum_cast only used to enum type` とエラーを吐いて終了する。

```cpp
static constexpr T operator| ( T t, U u ) {
  return enum_cast< T > :: to_enum( //enum class 型に戻す・・・(2)
    enum_cast< T > :: to_underlying( t ) | enum_cast< U > :: to_underlying( u )  //enum classの基底型にして計算・・・(1)
  ) ;
```

このように、演算を行う際はこの2つを組み合わせて利用する。





### is_same_underlying 

```cpp
template < typename T, typename U  > 
using is_same_underlying = std :: is_same<
    typename enum_cast< T > :: underlying_type 
  , typename enum_cast< U > :: underlying_type 
>;
```

2つの enum class を受け取って基底型が同一か確認する。
基底型の取得には `enum_cast<T> :: underlying_type` を利用している。
なので、`T` / `U` が enum class でない場合、エラーとなる。

今回2つの enum class 同士の計算ができるように作っているが、それは

```cpp
enum class status : unsigned char{
    off      = 0b00000000
  , standby  = 0b00000001
  , running  = 0b00000010
  , abort    = 0b00000011
  , overflow = 0b10000000
};

enum class mask : unsigned char {
    overflow = 0b10000000
  , status   = 0b00000011
};
```

このような enum class 同士の計算を想定している。








### is_enum_logic_safe

```cpp
template < typename... Args> 
class is_enum_logic_safe {

  private:
   
    // no args
    template < typename... InArgs > 
      struct impl : std :: false_type {};

    // single arg
    template < typename Type1, typename... Tail > 
      struct impl< Type1, Tail... > : std :: true_type {};

    // more than 2 args
    template < typename Type1, typename Type2, typename... Tail > 
      struct impl< Type1, Type2, Tail... > : 
      std :: conditional<
          is_same_underlying< Type1, Type2 > :: value 
        , typename impl< Type2, Tail... > :: type 
        , std :: false_type 
      > :: type  
      {};

  public:

    static constexpr bool value = impl< Args... > :: type :: value;
};
```

与えられたテンプレート引数同士で計算して問題がないかチェックする。

```cpp
// no args
template < typename... InArgs > 
  struct impl : std :: false_type {};
```

引数が何もない状態では計算も何もないので `std :: false_type`


```cpp
// more than 2 args
template < typename Type1, typename Type2, typename... Tail > 
  struct impl< Type1, Type2, Tail... > : 
  std :: conditional<
      is_same_underlying< Type1, Type2 > :: value 
    , typename impl< Type2, Tail... > :: type 
    , std :: false_type 
  > :: type  
  {};
```

引数が2つ以上の場合に呼ばれる。
先頭から順番に enum class の基底型が同一かをチェックする動作を実装した。

1つ目と2つ目、 enum class `Type1` / `Type2` の基底型が同一だった場合、
Type2 を先頭にしたパラメータパックでもう一度 `impl(...)` を呼び出す、
という処理が引数1つになるまで続く。

```cpp
// single arg
template < typename Type1, typename... Tail > 
  struct impl< Type1, Tail... > : std :: true_type {};
```

最初から引数が1つしか与えられなかった場合
: 基底型が一緒なのは自明なので `std :: true_type`


最後の1つまで基底型が一緒だった場合
:   ここでの `Type1` = 前ループの`Type2`、
    つまり最後の引数まで全て基底型が同一だったことになるので 
    `std :: true_type` を返してあげなければならない


```cpp
public:
static constexpr bool value = impl< Args... > :: type :: value;
```

処理結果を `value` として定義。これが返り値になる。







### enum_enabler

```cpp
template < typename T, typename U > 
using enum_enabler = std :: enable_if<
    enum_logic_enabled< T, U > :: value &&
    is_enum_logic_safe< T, U > :: value 
  , std :: nullptr_t
>;
```

各種 `operator` の第3引数に指定され、


```cpp
template < typename T , typename U = T, typename enum_enabler< T, U > :: type = nullptr > 
```


関数の有効/無効を制御する。 `std :: enable_if` は

```cpp
std :: enable_if<
  /* 条件 */
  /* trueの時の型 */
> :: type /* trueの場合のみ type が存在する */
```

というテンプレートメタ関数。なので

enum class `T` / `U` 間の計算を有効化(`enum_logic_enabled< T, U > :: value`) されており(後述)、かつ
enum class `T` / `U` 間で計算して問題ない(`is_enum_logic_safe< T, U > :: value`)時に限り、

```cpp
template < typename T , typename U = T, typename std :: nullptr_t = nullptr >
```

となり関数が有効化する。もし条件に合わない場合、

```cpp
template < typename T , typename U = T>
```

の探索が始まるが、定義されていない場合はエラーとなる。

```cpp
typename U = T
```

テンプレート引数が1つだけの場合、`U` には `T` が代入されるので、`T` 同士の計算となる。









### enum_logic_enabled

```cpp
template < typename T, typename U = T > 
struct enum_logic_enabled : std :: false_type {}; 
```

enum class 型 `T` と `U` 間の計算の有効/無効を定義する。タグディスパッチと呼ばれる手法。
有効化したい組み合わせを


```cpp
template <> 
struct enum_logic_enabled< A > : std :: true_type {};

template <> 
struct enum_logic_enabled< C > : std :: true_type {};

template <> 
struct enum_logic_enabled< A, C > : std :: true_type {};

template <> 
struct enum_logic_enabled< A, B > : std :: true_type {};
```

このように `std :: true_type` を継承させて特殊化させることで、
上記 `enum_enabler` の条件式が変化する。

ただし、有効化させても計算に問題がある( `is_enum_logic_safe<T, U> :: value = false` ) 場合、
関数は有効にならない。




### operator

```cpp
template < typename T , typename U = T, typename enum_enabler< T, U > :: type = nullptr > 
static constexpr T operator| ( T t, U u ) {
  return enum_cast< T > :: to_enum( 
    enum_cast< T > :: to_underlying( t ) | enum_cast< U > :: to_underlying( u ) 
  ) ;
}
```

ところどころバラバラになって出てきたがこのように書く。
他の演算子を追加するには、例えば `&` を追加する場合、

```cpp
template < typename T , typename U = T, typename enum_enabler< T, U > :: type = nullptr > 
static constexpr T operator& ( T t, U u ) {
  return enum_cast< T > :: to_enum( 
    enum_cast< T > :: to_underlying( t ) & enum_cast< U > :: to_underlying( u ) 
  ) ;
}
```

このようにするだけで有効化している組み合わせ全てで `operator&` の計算が有効化される。






### その他

テストケースの enum class
```cpp
enum class A : unsigned char { 
  one
};

enum class B : unsigned char {
  two
};

enum class C : unsigned int {
  three
};

```

main 関数内

```cpp
  A a;
  B b;
  C c;

  a = A :: one   | A :: one;
  //b = B :: two   | B :: two; 
  //Bの計算が有効化されていないためエラー
  c = C :: three | C :: three;


  a = A :: one | B :: two ;
  //a = A :: one | C :: three; 
  //A - C 間で計算は有効化されているが基底型が違うのでエラー
  
  unsigned char uc = 0;
  int i = 0;

  //enum_cast< int > :: type j = 0;
  //int は enum class ではないのでエラー

```

## 出力結果

なし

## 連絡事項

`namespace` に入れるなどは各自で。
