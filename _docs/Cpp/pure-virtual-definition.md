---
layout : post
title  : 純粋仮想関数は定義を持てる
date   : 2016/09/13
lastchange : 2017-06-15 16:56:59.
tags   :
  - C++
  - interface
  - pure
  - virtual
---

{% assign text='
GitHubで公開しているC++参考書に、以下のようなpull requestが送られてきた。

[Pure virtual function by daisukekoba · Pull Request #153 · EzoeRyou/cpp-book](https://github.com/EzoeRyou/cpp-book/pull/153)

純粋virtual関数は、宣言を分ければ、定義も持てるそうだ。実際にまともなC++コンパイラーでコンパイルしてみると、たしかにその通りだ。

どうやら、私の規格の文面の解釈が間違っていたらしい。

C++の規格に曰く、

> 10.4 paragraph 2
>
> A function declaration cannot provide both a pure-specifier and a definition.

ひとつの関数宣言はpure指定子と定義の両方を提供することができない。

これを読むと、以下のコードがエラーになることがわかる。

```cpp
struct S
{
// エラー、pure-specifierと定義を両方持つ
    virtual void f() = 0 { }
} ;
```

しかし、私は「ひとつの」という部分を見落としていた。つまり、複数の関数宣言を使えば、両方とも持てるのだ。

```cpp
struct S
{
// pure-specifier
    virtual void f() = 0 ;
} ;

// 定義
void S::f() { } 
```

規格の文面を正しく解釈すると、このコードは正しいはずで、実際に、既存のC++コンパイラーはこのコードを通す。 

' | markdownify %}
{% assign source='
[純粋仮想関数は定義を持てる](https://cpplover.blogspot.jp/2013/11/blog-post_24.html)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

上記記事では引用した場所以降も有用な検討をしているが、ひとまずここまでを実験してみた。

## header.h

```cpp
class C{

  public:

  void Fx(void) = 0;

};

class D : public C{

  public:
    void Fx(void);
};
```

## main.cpp

```cpp
#include <iostream>
using namespace std;

void C :: Fx(void){ cout << "C :: Fx" << endl; }
void D :: Fx(void){
  C :: Fx();
  cout << "D :: Fx" << endl;
}

int main(void){

  D d;
  d.Fx();

  return 0;
}
```

宣言と定義を別にしたら純粋仮想関数でも呼び出して実行できる。

インターフェイスがもつメンバ変数を初期化する際に便利かも。
