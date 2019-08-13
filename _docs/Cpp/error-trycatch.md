---
layout : post
title  : "エラー処理: C++ の例外処理"
date   : 2019/08/13
lastchange : 2019-08-14 01:46:06.
tags   :
  - C++
  - C++11
  - Error
  - try
  - catch
---

## 続・エラー処理をまとめようと思った

今度は C++ の例外処理を見ていく。

参考:

* [例外処理 - C++ 入門](http://kaitei.net/cpp/exceptions/)
* [stdexcept - cpprefjp C++日本語リファレンス](https://cpprefjp.github.io/reference/stdexcept.html)



## C++ の例外処理

```cpp
#include <iostream>
#include <exception>
#include <stdexcept> 

void Fx ( const int x ) {
  
  if( x < 0 ) 
    throw std :: out_of_range( "in Function Fx: x must bigger than 0" );

  std :: cout << "process of Fx" << std :: endl;
  return;
}

void Fx2 ( const int value ) {

  if( value > 100 )
    throw std :: invalid_argument( " in Function Fx2 : value must smaller than 100" );

  try{ 
    Fx( value );
  }
  catch( std :: exception& e ){ 
    std :: cerr << " in Function Fx2 : Fx call error\n" << e.what() << std :: endl;
    throw;
  }


  return;
  
}


int main( void ) {


  try{
    Fx( 10 );
  }
  catch( std :: exception& e ){ 
    std :: cerr << "error! try other value\n" << e.what() << std :: endl;
  }

  try{ 
    Fx2( -10 );
  }
  catch( std :: exception& e ) {
    std :: cerr << "call Fx2 error!" << std :: endl;
    std :: cerr << e.what() << std :: endl;
  }

  try{ 
    Fx2( 200 );
  }
  catch( std :: exception& e ){
    std :: cerr << "call Fx2 error!" << std :: endl;
    std :: cerr << e.what()          << std :: endl;
  }

  

  return 0;
}
```

コード解説の前に、前提知識



### try-catch 節

{% capture text %}

例外の検出と解決は，次の `try-catch` 文を用いて記述します。
`try` 節には，例外が発生しうる処理，`catch` 節には，例外が発生した場合に行う処理を記述します。

```cpp
try {
    // 例外が発生しうる処理
}
catch (例外型 引数) {  // 引数は省略可
    // 例外が発生した場合に行う処理
}
```

`try` 節の中で例外が発生すると，処理は直ちに対応する `catch` 節へ移ります。

`catch` 節は複数書くことができますが，実行されるのは例外型がマッチした最初の節だけです。
派生クラス型の例外は，その基底クラス型の `catch` 節にもマッチします。
例外型と引数の代わりに `...` と書くと，その `catch` 節は例外型を問いません。

{% endcapture %}
{% capture source %}
[例外処理 - C++ 入門](http://kaitei.net/cpp/exceptions/)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`throw` する型は任意に決めることができるが、 C++ には以下の例外クラスが標準で規定されている。
容量とかオーバーヘッドなどが気にならない場合、以下のクラスのいずれか、またはそれから派生したクラスを利用するのがよい。



### `std :: exception` 派生クラス

{% capture text %}

`<stdexcept>` ヘッダは、標準的な例外クラスを提供する。
これらの例外クラスは、標準ライブラリ内でも使用されている。

これらのクラス群に反映されているエラーモデルでは、エラーは「実行時エラー (runtime error) 」と「論理エラー (logic error) 」との２つに大別される。
論理エラーを区別するための性質は、それがプログラムの内部的論理の誤りに起因することである。理論的には、それらは予め避けることができる。
対して、実行時エラーはプログラムの制御の及ばない事象に起因する。それらを前もって簡単に予見することはできない。

このヘッダで提供される例外クラスは以下の表の通りである。以下のクラスは全て、`std` 名前空間で提供される。

| 名前               | 説明                                                         | 対応バージョン |
|--------------------|--------------------------------------------------------------|----------------|
| `logic_error`      | プログラムの実行前に検出可能なエラー（論理エラー）を示す     |                |
| `domain_error`     | 定義域エラーを示す                                           |                |
| `invalid_argument` | 不正な引数を示す                                             |                |
| `length_error`     | 長すぎるオブジェクトを作ろうとしたことを示す                 |                |
| `out_of_range`     | 引数が許容範囲外であることを示す                             |                |
| `runtime_error`    | プログラム実行時にのみ検出可能なエラー（実行時エラー）を示す |                |
| `range_error`      | 内部計算によって、値が範囲外になったことを示す（値域エラー） |                |
| `overflow_error`   | 数値計算の結果がオーバーフローしたことを示す                 |                |
| `underflow_error`  | 数値計算の結果がアンダーフローしたことを示す                 |                |

例外クラスには継承関係があり、以下の箇条書きの階層構造で示す。

* `exception`
  * `logic_error`
      * `domain_error`
      * `invalid_argument`
      * `length_error`
      * `out_of_range`
    * `runtime_error`
      * `range_error`
      * `overflow_error`
      * `underflow_error`

{% endcapture %}
{% capture source %}
[stdexcept - cpprefjp C++日本語リファレンス](https://cpprefjp.github.io/reference/stdexcept.html)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

### コード詳細


```cpp
#include <iostream>
#include <exception>
#include <stdexcept> 
```

`exception` ヘッダは `std :: exception` を利用するために、
`stdexcept` ヘッダは例外処理クラスを利用するために include している。

```cpp
void Fx ( const int x ) {
  
  if( x < 0 ) 
    throw std :: out_of_range( "in Function Fx: x must bigger than 0" );

  std :: cout << "process of Fx" << std :: endl;
  return;
}
```

関数 `Fx` では、引数 `x` が 0 未満だった場合、エラーとして `std :: out_of_range` クラスを投げている。
`std :: out_of_range` への引数は、何が起きたかを記述している。

```cpp
void Fx2 ( const int value ) {

  if( value > 100 )
    throw std :: invalid_argument( " in Function Fx2 : value must smaller than 100" );
```

関数 `Fx2` その 1 。101 以上の引数の場合、 `std :: invalid_argument` クラスをエラーとして投げている。
こちらもその引数にはエラーの詳細を入れる。


```cpp
  try{ 
    Fx( value );
  }
  catch( std :: exception& e ){ 
    std :: cerr << " in Function Fx2 : Fx call error\n" << e.what() << std :: endl;
    throw;
  }


  return;
  
}
```

関数 `Fx2` その 2 。内部で `Fx` を呼び出している。 `Fx` が `std :: out_of_range` 例外を投げる可能性があるので、
その例外処理をするために `try` `catch` を記述している。

この時 `catch` の引数が親クラスとなる `std :: exception` の参照になっているが、子クラスをちゃんとここで処理できる。
ただし、継承/オーバーライドされているものを除き、子クラスで定義されているメンバ関数や変数は利用できない。

`e.what()` 関数で例外クラスの引数に入れた文字列を取得することができる。

`throw` で今捕捉した `try` ブロック上位の `try` ブロックへ、例外を更に投げることができる。
この時特に投げる変数を指定しない場合、捕捉した変数をそのまま上位 `try` に譲り渡す。

```cpp
int main( void ) {


  try{
    Fx( 10 );
  }
  catch( std :: exception& e ){ 
    std :: cerr << "error! try other value\n" << e.what() << std :: endl;
  }

  try{ 
    Fx2( -10 );
  }
  catch( std :: exception& e ) {
    std :: cerr << "call Fx2 error!\n" << e.what() << std :: endl;
  }

  try{ 
    Fx2( 200 );
  }
  catch( std :: exception& e ){
    std :: cerr << "call Fx2 error!\n" << e.what() << std :: endl;
  }

  

  return 0;
}
```

main 関数。　先程までに定義した関数を呼び出している。
呼び出す時それぞれで例外を受け取るため `try` `catch` ブロックを用意している。

実行結果はこちら

```
process of Fx
 in Function Fx2 : Fx call error
in Function Fx: x must bigger than 0
call Fx2 error!
in Function Fx: x must bigger than 0
call Fx2 error!
 in Function Fx2 : value must smaller than 100
Program ended with exit code: 0
```

