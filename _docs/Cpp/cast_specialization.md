---
layout : post
title  : テンプレート型変換演算子を特殊化する
date   : 2017/07/31
lastchange : 2018-07-31 19:20:08.
tags   :
  - C++
  - C++11
  - type transform operator
  - cast operator
  - specialization
  - 型変換演算子
  - キャスト演算子
---

## 型変換演算子を特殊化するには

これが気になったので試してとりあえず動いた時の結果を載せておく。

## 結局はオーバーロード

```cpp
 template < typename Type > 
  operator Type() const { 
   
    std :: cout << "genelic function" << std :: endl;

    return std :: sqrt( _x * _x + _y * _y ) ;  
  }

  // Specialization
  operator std :: pair< T, T >() const {

    std :: cout << "specialization" << std :: endl;

    return std :: pair< T, T >( _x, _y ) ; 
  }
```

こうなる。普通に型変換演算子をオーバーロードするだけ。

例題として `std :: pair` 型のオーバーロードを実装した。


## main.cpp

```cpp
#include <iostream>
#include <cmath> 

template < typename T > 
struct vec2{
  
  T _x;
  T _y;

public:
  
   vec2 ( void ) = default;
  ~vec2 ( void ) = default;

  vec2 ( const vec2< T >& other ) : 
    _x( other._x ), _y( other._y )
  {}

  vec2 ( const T& x, const T& y ) : 
    _x( x ), _y( y ) 
  {}

  vec2 ( const std :: pair< T, T >& pair ) : 
    _x( pair.first ), _y( pair.second ) 
  {}
  

  template < typename Type > 
  operator Type() const { 
   
    std :: cout << "genelic function" << std :: endl;

    return std :: sqrt( _x * _x + _y * _y ) ;  
  }

  operator std :: pair< T, T >() const {

    std :: cout << "specialization" << std :: endl;

    return std :: pair< T, T >( _x, _y ) ; 
  }

};


//exclude T* instance
template < typename T > 
struct vec2< T* >;



int main(int argc, const char * argv[]) {

  
  
  vec2< int > v1( 10, 10 );

  std :: cout << "----transform to int----" << std :: endl;
  
  int power = v1;


  std :: cout << "----transform to std :: pair< int, int >----" << std :: endl;
  
  std :: pair< int , int > p = v1;


  std :: cout << "----output----" << std :: endl;
  
  std :: cout << power << std :: endl;
  std :: cout << p.first << ":" << p.second << std :: endl;


  std :: cout << "----transform to float----" << std :: endl;


  vec2< float > vf( 31, 1.2 );
  std :: cout << static_cast< float >( vf ) << std :: endl;

  return 0;
}
```

* 2軸のベクトルデータ
* 単一の型へ変換される場合は長さを得ることができる
* `std :: pair` 型の場合は2軸のデータを得る

的な実装。


## Output

```
----transform to int----
genelic function
----transform to std :: pair< int, int >----
specialization
----output----
14
10:10
----transform to float----
genelic function
31.0232
```
