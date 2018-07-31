---
layout : post
title  : "std :: conditional の判定式でエラー"
date   : 2017/07/31
lastchange : 2018-07-31 19:53:06.
tags   :
  - C++
  - C++11
  - type_traits
  - using
  - alias template
---

## Template argument for non-type template parameter must be an expression

```cpp
template < typename T > 
using is_C__ = typename std :: conditional< 
    is_C_v< T >
  , std :: true_type
  , std :: false_type 
> :: type;
```

こんなコードで上記エラーが `is_C_v< T >` で発生した。
コレ以外に特にエラーを吐いていない。 


## `using` で型のエイリアスを指定していなかった

ちなみに `is_C_v< T >` はエイリアステンプレート。なんじゃろかと思って定義を見に行ったら

```cpp
template < typename T > 
using is_C_v = typename std :: is_same< T, C > :: value ;
```

ポカミスをしていた。

`using` は型のエイリアスなのに最後に勢いで `:: value` をつけてしまっているので

```cpp
is_C_v = true/false;
```

となり、そもそも定義が間違っていて代入できないはずだが、それでも `is_C_v` 側にエラーが出ない。
で、 `is_C_v` 型なので `bool` に型変換できないと怒られている、という状態だった。





## `using` 単体の場合はエラーが出る


一方、単体で型として利用すると、

```cpp
typeid( is_C_v< C >);
```

```
Typename specifier refers to non-type member 'value' in 'std::__1::is_same<C, C>'
```

ちゃんと `value` は型じゃないと怒られた。

単体テスト大事。



## 一方型ではなく `value` を保持したい時は

```cpp
consexpr bool is_C = typename std :: is_same< T, C > :: value ;
```

特に難しく考えることもなく、 `bool` で拾ってあげればよい。
