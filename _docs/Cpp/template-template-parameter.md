---
layout : post
title  : テンプレートテンプレートパラメータまとめ
date   : 2018/08/06
lastchange : 2018-08-07 01:39:22.
tags   :
  - C++
  - C++11
  - template template
  - specialization
  - 特殊化
  - テンプレートテンプレートパラメータ
---

## 複雑奇怪なテンプレートテンプレートパラメータの世界

頭がどうにかなりそうなのでまとめる。





## テンプレートテンプレートパラメータの基本

```cpp
template < template < typename > class C > 
struct test {};
```

このように、テンプレートパラメータの中にテンプレート型の情報を入れるとテンプレート型を受け取ることができる。
実際にこの `test` にテンプレート型を渡してみる。


```cpp
template < typename T > 
struct A {};

...

test< A< int > >; //Error
```

このような渡しかたは間違い。以下のようなエラーが出る。

```
Template argument for template template parameter must be a class template or type alias template
```

これが正解

```cpp
test< A >; //OK
```

なぜだろうか。


`A< int >` を渡してしまった場合、それはすでにテンプレート型ではなく `A< int >` 型になっている。
テンプレートテンプレートパラメータは、テンプレート型を受け取るのであって、型を受け取るのではない。

テンプレート型を保ったまま渡すには `A` を渡す必要がある。
受け取ったテンプレート型は、内部でテンプレート型から型に変換されて利用されるものであることを意識する。

```cpp
template < template < typename T > class C > 
test {

  C<int>;
  
  ...

  C<float>;
  ...
};
```





## `A< int >` などを渡したい場合はどうすればいいか

`A< int >` はすでに型である。テンプレート型ではない。ということに注目すればこうなることが予測できるはず。

```cpp
template < typename T > 
struct test {};

template < typename T > 
struct A {};

...

test< A< int > >; // OK
```

普通の型として受け取ってしまえばよい。






## テンプレートテンプレートパラメータとしての特殊化

テンプレート型を受け取る部分は、テンプレート型として特殊化しなければならない。すなわち

```cpp

template < typename T, typename U > 
struct A {};

template < typename T, typename U > 
struct B {};

...

template < template < typename, typename > class Temp > 
struct test{
  static constexpr bool value = false;
};


//Aで特殊化
template <> 
struct test < A >{  
  static constexpr bool value = true;
};

...


std :: cout << test < A > :: value << std :: endl; //true
std :: cout << test < B > :: value << std :: endl; //false
```

このような方法で特殊化することができる。





## 非型を含むテンプレートテンプレートパラメータの場合

通常のテンプレートテンプレートパラメータとほとんど同じように利用できる。

```cpp
template < int N, typename T > 
struct A {  };


template < template < int, typename > class Type >
struct test {  };


...

test< A >; // OK
```

テンプレート型の第1パラメータが `int` 型に変化し、
テンプレートテンプレートパラメータも `template < int, typename > class Type`
と、第1パラメータに `int` を受け取るように変化している。

```cpp

template < int N, typename T > 
struct A {  };

template < template < typename, typename > class Type >
struct test2 {  };

...

test2< A >; //Error
```

これはできない。以下のようなエラーが出る。

```
Template template argument has different template parameters than its corresponding template template parameter
```



## 非型テンプレートテンプレートパラメータにエイリアステンプレートとか継承とか

上記の

```cpp
template < template < int, typename > class Type >
struct test {  };
```

に、以下のようなものを渡すことができるだろうか


```cpp
//基底クラス
template < typename T, typename U > 
struct A {  };


//継承しつつ、第1パラメータを int 固定
template < typename T > 
struct inherit_A : A< int, T > {  };


//エイリアステンプレートで第1パラメータを int 固定
template < typename T > 
using using_A = A< int, T >;


template < template < int, typename > class Type >
struct test {  };
```

ぱっと見ではいけそうな気がするが、


```cpp
test< inherit_A >; //Error
test< using_A >;   //Error
```

両方とも不可能。

```
Template template argument has different template parameters than its corresponding template template parameter
```

パラメータを固定したとしても `template < typename T, typename U >` な
テンプレート型であるとみなされていることになるのだろうか。
であるならば `test` を元に戻したら問題なく動作するはずだが、

```cpp

template < template < typename, typename > class Type >
struct test {  };

...

test< inherit_A >; //Error
test< using_A >;   //Error
```

これも動作しない。

```
Template template argument has different template parameters than its corresponding template template parameter
```

同様のエラーが出る。となると

```cpp
template < typename Type >
struct test {  };


....

test< inherit_A >; //Error
test< using_A >;   //Error

```

これもエラー。だが、

```
Use of class template 'inherit_A' requires template arguments
Use of alias template 'using_A' requires template arguments
```

文章が変化した。テンプレートパラメータが足りない。

```cpp
template < typename Type >
struct test {  };


....

test< inherit_A <char> >; //OK
test< using_A   <long> >; //OK
```

これは通る。テンプレートパラメータを1つでも確定させると、
もうテンプレート型ではなく型になってしまうようだ。
だが型としても不十分で、完全な型になるために
テンプレートパラメータが足りていないとエラーが出ていたみたいだ。




## テンプレートテンプレートパラメータの名前は利用できない

どういうことかというと、

```cpp

template < template < typename T , typename U > class C > 
struct test {

  T type;  //Error
  U type2; //Error
};
```

内側の山カッコ内の名前は利用できない。よくよく考えてみると

```cpp
template < typename T > 
struct A {
  T type; //OK
};

T global_type; //NG
```

当たり前だが、クラスや関数内でテンプレートパラメータを利用できるが外側では利用できない。
今回も `template < typename T , typename U > class C` の外側でテンプレートパラメータを利用しようとしているので、
それは無理だということらしい。





## `Temp<...>` 型で特殊化する

```cpp
template < typename T > 
struct test {  };
```

を、

```cpp
template < typename T > 
struct A {  };

test < A < int   > >;
test < A < char  > >;
test < A < float > >;
```

と、 `A<...>` といった形で特殊化したい場合、どうすればよいか。

```cpp

//基底クラスA
template < typename T, typename U > 
struct A {};

//第1パラメータの int 固定
template < typename T > 
using int_A = A< int, T >;


//A <int, float> 型
using int_float_A = A< int, float >;


//別の基底クラスB
template < typename T, typename U > 
struct B {  };


//testの基底
template < typename T > 
struct test { 
  static constexpr bool value = false;
};


//Temp<int, ...> で特殊化
template < template < typename, typename > class Temp, typename Type > 
struct test < Temp< int, Type>  > { 
  static constexpr int value = 10;
};


//Temp<iint, float> で特殊化
template < template < typename, typename > class Temp > 
struct test < Temp< int, float > > { 
  static constexpr int value = -1;
};

// B<...> で特殊化
template < typename T, typename U > 
struct test < B< T, U > >{ 
  static constexpr int value = 100;
};



int main(int argc, const char * argv[]) {


  std :: cout << test < A< char, char > > :: value << std :: endl; //testの基底呼び出し
  std :: cout << test < int_A< int > >    :: value << std :: endl; //Temp<int, ...> 特殊化呼び出し
  std :: cout << test < int_float_A >     :: value << std :: endl; //Temp<int, float> 特殊化呼び出し

  std :: cout << test < B< float, float > > :: value << std :: endl; //B<...> 特殊化呼び出し
  std :: cout << test < B< char, char   > > :: value << std :: endl; //B<...> 特殊化呼び出し

  return 0;
}
```

テンプレート型がひな形になった型の特殊化、ということで、まずどのようなテンプレート型がひな形になっているか指定する。

```cpp
template < template < typename, typename > class Temp, ... > 
```

ここでいくつのテンプレートパラメータ、非型テンプレートパラメータを持つテンプレート型がひな形であるかが確認できる。

次にテンプレートパラメータのまま残しておくパラメータを、特殊化のテンプレートパラメータとして取得する。
これが必要なのは、前述のようにテンプレートテンプレートパラメータのパラメータ名は利用できないため。

```cpp
template < template..., typename Type >
```

ここまでを指定して、どのようなテンプレート型 派生の型に対する特殊化であるかを明示する。

```cpp
struct test < Temp< int, Type  > > ...
struct test < Temp< int, float > > ...
```


そもそも、あるテンプレート型がひな形の場合、という特殊化はこうなる。

```cpp
template < typename T, typename U > 
struct test < B< T, U > >{ 
  static constexpr int value = 100;
};
```




で、完全な型でなければ呼び出しが行なえないので、完全な型にして呼び出しを行う。

```cpp
std :: cout << test < A< char, char > >   :: value << std :: endl; //基底の呼び出し
std :: cout << test < int_A< int > >      :: value << std :: endl; // Temp<int, ...>   型の特殊化
std :: cout << test < int_float_A >       :: value << std :: endl; // Temp<int, float> 型の特殊化

std :: cout << test < B< float, float > > :: value << std :: endl; // B<...> 型の特殊化
std :: cout << test < B< char, char   > > :: value << std :: endl; 
```


テンプレート型を受け取ることはできないのでこういうのはエラーとなる。

```cpp
std :: cout << test< A > :: value << std :: endl;
```



他の特殊化と区別をつけることができない場合、曖昧で解決できないというエラーが起こる。

```cpp
std :: cout << test < B< int, char > > :: value << std :: endl; 
```

この場合、`Temp<int, ...>` の特殊化と `B<...>` の特殊化、どちらを使えばいいか判断できなくてエラーが出る。

```
Ambiguous partial specializations of 'test<B<int, char> >'
```


### 実行結果

```
0
10
-1
100
100
```

