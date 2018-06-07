---
layout : post
title  : ラムダ式を利用する
date   : 2017/06/06
lastchange : 2018-06-07 11:19:51.
tags   :
  - C++
  - C++11
  - Lamda
---

## そろそろC++11も普及しているようだし、ラムダ式を使ってみる

{% capture text %}
「ラムダ式(lambda expressions)」は、簡易的な関数オブジェクトをその場で定義するための機能である。

この機能によって、「高階関数(関数を引数もしくは戻り値とする関数)」をより使いやすくできる。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ラムダ式 - cpprefjp C++日本語リファレンス](https://cpprefjp.github.io/lang/cpp11/lambda_expressions.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


javascriptの

```js
((function(){
})());
```

みたいなことができるようになったということなので試してみる。

参考

* [ラムダ式 - cpprefjp C++日本語リファレンス](https://cpprefjp.github.io/lang/cpp11/lambda_expressions.html)
* [ラムダ式 - C++入門](http://kaworu.jpn.org/cpp/%E3%83%A9%E3%83%A0%E3%83%80%E5%BC%8F)
* [C++11のラムダ関数の簡単なまとめ - minus9d's diary](http://minus9d.hatenablog.com/entry/2015/03/08/232143)
* [C++ のラムダ式を型推論しながら関数ポインタ型で受け取る - Secret Garden(Instrumental)](http://secret-garden.hatenablog.com/entry/2017/08/12/201711)




## 作って即利用する

```cpp
#include <iostream>

int main(int argc, const char * argv[]) {

  [](){ std :: cout << "Lamda" << std :: endl; }();

  return 0;
}
```

出力結果

```
Lamda
Program ended with exit code: 0
```


{% capture text %}

### ラムダ式の文法

```cpp
int main(int argc, char *argv[]) {
	[]	// ラムダキャプチャー
	()	// パラメータ定義節
	{}	// 複合ステートメント
	()	// 関数呼び出し式
	;
	return 0;
}
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ラムダ式 - C++入門](http://kaworu.jpn.org/cpp/%E3%83%A9%E3%83%A0%E3%83%80%E5%BC%8F)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

というように、`[](){}` の3つの括弧で関数を宣言する。
それを実行するために、お尻に`()`がくっついている。

## 返り値を明示する

通常で返り値は自動的に判断してくれる。

```cpp
#include <iostream>

int main(int argc, const char * argv[]) {

  std :: cout << [](){ return 3299342; }() << std :: endl;
  
  return 0;
}
```

出力結果

```
3299342
Program ended with exit code: 0
```


返り値を明示することができる。


```cpp
#include <iostream>

int main(int argc, const char * argv[]) {

  std :: cout << []() -> float { return 3299342; }() << std :: endl;
  
  return 0;
}
```

出力結果


```
3.29934e+06
Program ended with exit code: 0
```

{% capture text %}


### 戻り値の型の指定

戻り値の型を指定するには以下のようにします。

```cpp
auto f1_double = [](int x) -> double { return x * x; };
std::cout << "10000: " << f1_double(10000) << std::endl;  // "10000: 1e+08"と出力される
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[C++11のラムダ関数の簡単なまとめ - minus9d's diary](http://minus9d.hatenablog.com/entry/2015/03/08/232143)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


## 変数に入れる

javascriptの`function`同様、変数に代入することができる。

```cpp
#include <iostream>

int main(int argc, const char * argv[]) {

  auto fx = [](int i){ std :: cout << i << std :: endl; };

  fx(100);

  return 0;
}

```

出力結果

```
100
Program ended with exit code: 0
```

明示的に型指定をした `std :: function` オブジェクトに代入することもできる。
`std :: function`を利用する場合、 `#include <functional>` が必要。

```cpp
#include <iostream>
#include <functional>

int main(int argc, const char * argv[]) {

  std :: function<void(int)> fx = [](int i){ std :: cout << i << std :: endl; };

  fx(100);

  return 0;
}
```


出力結果


```
100
Program ended with exit code: 0
```

{% capture text %}

# std::function

* ラムダ式を変数に保存できるのが、`std::function` 。関数ポインタの次世代版
* ただ、基本は `auto` で受け取ること（`std::function` によって型消去されないため、autoの方が早い）

## 使い方

```cpp
std::function< 戻り値の型(引数の型) > f = ラムダ式;

std::function< int(void) > f = []{ return 0; };
f(); // 0が返る
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[C++11 ラムダ式、std::function - Qiita](https://qiita.com/rokuta96/items/13e69bdab0eacfa33940)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


一応、関数ポインタに代入することも可能


```cpp

#include <iostream>

int main(int argc, const char * argv[]) {

    void (*fx)(int) = [](int i){ std :: cout << i << std :: endl; };

  fx(100);

  return 0;
}
```

出力結果

```
100
Program ended with exit code: 0
```

{% capture text %}


## ラムダ式を関数ポインタ型へ変換する

これは割と一般的な知識だと思うんですが、『キャプチャしていない』ラムダ式は関数ポインタ型へとキャストする事が出来ます。

```cpp
using func_t = int(*)(int);
// 関数ポインタ型として受け取る事が出来る
func_t twice = [](int n){ return n + n; };
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[C++ のラムダ式を型推論しながら関数ポインタ型で受け取る - Secret Garden(Instrumental)](http://secret-garden.hatenablog.com/entry/2017/08/12/201711)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ただし、これらはキャプチャーという機能を有効にすると利用できないらしい。







## キャプチャ(外部の変数)を利用する

外部の変数を利用(キャプチャ)できるようにすることができる。




```cpp
#include <iostream>
#include <string>

int main(int argc, const char * argv[]) {

  std :: string str("string");
  
  str = [=](){ return str + " lamda string"; }();

  std :: cout << str << std :: endl;


  return 0;
}
```

実行結果

```
string lamda string
Program ended with exit code: 0
```

これができると返り値があるブロック `{ ... }` のようなものができて、一連の処理とその場限りで利用する変数をまとめるのに有効な気がする。

外部の変数を参照型でキャプチャすることもできる。

```cpp
#include <iostream>
#include <string>

int main(int argc, const char * argv[]) {

  std :: string str("string");
  
  [&](){ str = "lamda string"; return str; }();

  std :: cout << str << std :: endl;


  return 0;
}
```

実行結果

```
lamda string
Program ended with exit code: 0
```

他にもさまざまな指定方法があるが、参照渡しの場合変数の寿命とか、値渡しの場合は変数のサイズとか、きにしなければならない問題がある。

{% capture text %}

### キャプチャ

ラムダ式には、ラムダ式の外にある自動変数を、ラムダ式内で参照できるようにする「キャプチャ(capture)」という機能がある。
キャプチャは、ラムダ導入子(lambda-introducer)と呼ばれる、ラムダ式の先頭にある `[ ]` ブロックのなかで指定する。

キャプチャには、コピーキャプチャと参照キャプチャがあり、デフォルトでどの方式でキャプチャし、個別の変数をどの方式でキャプチャするかを指定できる。


|キャプチャ記法 | 説明                                                                            |
| ------------- | ------------------------------------------------------------------------------- |
| `[&]`         | デフォルトで環境にある変数を参照して、ラムダ式のなかで使用する                  |
| `[=]` 	      | デフォルトで環境にある変数をコピーして、ラムダ式のなかで使用する                |
| `[&x]` 	      | 変数xを参照して、ラムダ式のなかで使用する                                       |
| `[x]` 	      | 変数xをコピーして、ラムダ式のなかで使用する                                     |
| `[&, x]` 	    | デフォルトで参照キャプチャ、変数 `x` のみコピーして、ラムダ式のなかで使用する   |
| `[=, &x]` 	  | デフォルトでコピーキャプチャ、変数 `x` のみ参照して、ラムダ式のなかで使用する   |
| `[this]` 	    | `*this` のメンバを参照して、ラムダ式のなかで使用する                            |
| `[this, x]`   | `*this` のメンバを参照し、変数 `x` のみコピーして、ラムダ式のなかで使用する     |


---


### mutable

キャプチャした変数はクロージャオブジェクトのメンバ変数と見なされ、クロージャオブジェクトの関数呼び出し演算子は、
デフォルトで `const` 修飾される。そのため、コピーキャプチャした変数をラムダ式のなかで書き換えることはできない。

```cpp
int rate = 2;
[rate](int x) -> int { return x * ++rate; } // エラー！rate変数を書き換えることはできない
```

コピーキャプチャした変数を書き換えたい場合は、ラムダ式のパラメータリストの後ろに `mutable` と記述する。

```cpp
int rate = 2;
[rate](int x) mutable -> int { return x * ++rate; } // OK
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ラムダ式 - cpprefjp C++日本語リファレンス](https://cpprefjp.github.io/lang/cpp11/lambda_expressions.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

いろいろ調べてみたが今の所 `const &` 型でキャプチャする方法はなさそう。


## 返り値や引数をラムダ式にする


もちろん、返り値や引数にラムダ式を渡すことができる。

```cpp
#include <iostream>
#include <functional>

std :: function<const int(const int)> Fx( const std :: function<const int(const int)> &_fx );


int main(int argc, const char * argv[]) {

  int var = Fx([](const int x){ 
      return x; 
  })(200);

  std :: cout << "return function : " << var << std :: endl;
  
  return 0;
}

std :: function<const int(const int)> Fx( const std :: function<const int(const int)> &_fx ){

  int x = _fx(100);

  std :: cout << "argument function : " << x << std :: endl;

  return [=](const int y) -> const int { return x + y; };
}
```



実行結果




```
argument function : 100
return function : 300
Program ended with exit code: 0
```

キャプチャ機能を利用しない場合に限り、関数ポインタで渡すこともできる。

```cpp

#include <iostream>
#include <functional>

const int (*Fx (const int (*_fx) (const int) ) ) (const int);

int main(int argc, const char * argv[]) {

  int var = Fx([](const int x) -> const int { return x; })(200);

  std :: cout << "return function : " << var << std :: endl;
  
  return 0;
}

const int (*Fx(const int (*_fx) (const int) ) ) (const int){



  std :: cout << "argument function : " << _fx(100) << std :: endl;

  return [](const int y) -> const int { return y; };

}
```




実行結果




```
argument function : 100
return function : 200
Program ended with exit code: 0
```

キャプチャ機能を利用してラムダ式を返り値にしようとすると、こんなエラーが出る

```
No viable conversion from returned value of type '(lambda at main.cpp:48:10)' to function return type 'const int (*)(const int)'
```

