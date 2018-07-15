---
layout : post
title  : has_trivial_assign は is_trivially_copy_assignable になった
date   : 2017/07/15
lastchange : 2018-07-15 12:36:06.
tags   :
  - C++
  - C++11
  - boost
  - has_trivial_assign
  - has_trivial_copy_assign
  - has_trivially_copy_asign
---

## boost :: has_trivial_assign は C++11 のどれ当たるのか

`std :: has_trivial_assign` が 

```err 
No member named 'has_trivial_assign' in namespace 'std'
```

と言われるので調べてみた。

参考:

* [Visual C++ 2015 での互換性に影響する変更点](https://msdn.microsoft.com/ja-jp/library/bb531344.aspx)
* [traits](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3142.html)
* [本の虫: pre-Batavia mailingの簡易レビュー](https://cpplover.blogspot.com/2010/10/pre-batavia-mailing.html)
* [gcc/ChangeLog-2010 at master · gcc-mirror/gcc](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/ChangeLog-2010)



## 名称変更のログ

{% capture text %}
以前のバージョンの C++ ドラフト標準の型の特徴の古い名前が削除されました。
これらは C++11 で変更されており、Visual Studio 2015 では C++11 値に更新されました。
古い名前と新しい名前を次の表に示します。

| 古い名前                        | 新しい名前                         |
|---------------------------------|------------------------------------|
| has_default_constructor         | is_default_constructible           |
| has_copy_constructor            | is_copy_constructible              |
| has_move_constructor            | is_move_constructible              |
| has_nothrow_constructor         | is_nothrow_default_constructible   |
| has_nothrow_default_constructor | is_nothrow_default_constructible   |
| has_nothrow_copy                | is_nothrow_copy_constructible      |
| has_nothrow_copy_constructor    | is_nothrow_copy_constructible      |
| has_nothrow_move_constructor    | is_nothrow_move_constructible      |
| has_nothrow_assign              | is_nothrow_copy_assignable         |
| has_nothrow_copy_assign         | is_nothrow_copy_assignable         |
| has_nothrow_move_assign         | is_nothrow_move_assignable         |
| has_trivial_constructor         | is_trivially_default_constructible |
| has_trivial_default_constructor | is_trivially_default_constructible |
| has_trivial_copy                | is_trivially_copy_constructible    |
| has_trivial_move_constructor    | is_trivially_move_constructible    |
| has_trivial_assign              | is_trivially_copy_assignable       |
| has_trivial_move_assign         | is_trivially_move_assignable       |
| has_trivial_destructor          | is_trivially_destructible          |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Visual C++ 2015 での互換性に影響する変更点](https://msdn.microsoft.com/ja-jp/library/bb531344.aspx)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


まず Visual Studio に関する名称変更の記事が見つかった。
大体表のように名前変更がなされているようだが、`has_trivial_assign` から
`is_trivially_copy_assignable` は名称に大きな差異がある。
どうしてそうなった。

{% capture text %}
Summary of renamed traits 	

|              | General form                                                        | Nothrow form                                                                 | Trivial form                                                                   |
|--------------|---------------------------------------------------------------------|------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| Construction | **is\_constructible**                                               | **is\_nothrow\_constructible**                                               |                                                                                |
|              | **~~has~~** **_is_** **\_default\_construct** **_ible_** **~~or~~** | **~~has~~** **_is_** **\_nothrow\_default\_construct** **_ible_** **~~or~~** | **~~has~~** **_is_** **\_trivially\_default\_construct** **_ible_** **~~or~~** |
|              | **~~has~~** **_is_** **\_copy\_construct** **_ible_** **~~or~~**    | **~~has~~** **_is_** **\_nothrow\_copy\_construct** **_ible_** **~~or~~**    | **~~has~~** **_is_** **\_trivially\_copy\_construct** **_ible_** **~~or~~**    |
|              | **~~has~~** **_is_** **\_move\_construct** **_ible_** **~~or~~**    | **~~has~~** **_is_** **\_nothrow\_move\_construct** **_ible_** **~~or~~**    | **~~has~~** **_is_** **\_trivially\_move\_construct** **_ible_** **~~or~~**    |
| Destruction  |                                                                     |                                                                              | **~~has~~** **_is_** **\_trivially\_destruct** **_ible_** **~~or~~**           |
| Assignment   | **~~has~~** **_is_** **\_copy\_assign** **_able_**                  | **~~has~~** **_is_** **\_nothrow\_copy\_assign** **_able_**                  | **~~has~~** **_is_** **\_trivially\_copy\_assign** **_able_**                  |
|              | **~~has~~** **_is_** **\_move\_assign** **_able_**                  | **~~has~~** **_is_** **\_nothrow\_move\_assign** **_able_**                  | **~~has~~** **_is_** **\_trivially\_move\_assign** **_able_**                  |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[traits](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3142.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

{% capture text %}
[N3142: Adjustments to constructor and assignment traits](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3142.html)

`has_*_constructor` と `has_*_assign` という traits は、あたかもコア言語的な響きがする。
しかし、ライブラリ作者としては、ある型Tがコンストラクターを持っているかどうかは、別にどうでもよい。
ライブラリ作者が知りたいこととは、ある型 `T` に対して、`T()` とか、 `T(U)` などと書いてよいかどうかである。
したがって、`has` という名前は相応しくない。

すべての `has_*_constructor` と `has_*_assign` という名前の traits を、 `is_*_constructible` と、 `is_*_assignable` という名前に変える提案。 
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[本の虫: pre-Batavia mailingの簡易レビュー](https://cpplover.blogspot.com/2010/10/pre-batavia-mailing.html)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

この提案が採用されて `is_trivially_copy_assignable` などに名称変更されたのがわかる。
その前は `has_trivial_copy_assign` だったらしい。ならば `has_tirivial_assign` から 
`has_trivial_copy_assign` に、どこかで名称変更しているはず。そのポイントを探してみる。

{% capture text %}

2010-06-08  Paolo Carlini  <paolo.carlini@oracle.com>

* include/std/type_traits (has_nothrow_assign): Rename to
	has_nothrow_copy_assign.
	(has_trivial_assign): Rename to has_trivial_copy_assign.
* testsuite/20_util/has_nothrow_copy_assign/value.cc: New.
* testsuite/20_util/has_nothrow_copy_assign/requirements/
  typedefs.cc: Likewise.
* testsuite/20_util/has_nothrow_copy_assign/requirements/
	explicit_instantiation.cc: Likewise.
* testsuite/20_util/has_trivial_copy_assign/value.cc: Likewise.
* testsuite/20_util/has_trivial_copy_assign/requirements/
	typedefs.cc: Likewise.
* testsuite/20_util/has_trivial_copy_assign/requirements/
	explicit_instantiation.cc: Likewise.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[gcc/ChangeLog-2010 at master · gcc-mirror/gcc](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/ChangeLog-2010)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

これも <open-std.org> から探したかったが見つけられなかった。代わりに GCC の更新履歴に該当する箇所があった。

```
(has_trivial_assign): Rename to has_trivial_copy_assign.
```

ここまでひとまず `has_trivial_assign` は `is_trivially_copy_assignable` に名称変更されているのがほぼ決定した。

Boost で未だに `boost :: has_trivial_assign` なのは `has_trivial_copy_assign` になる前から開発されている名残か。
ここまでの流れからすると、 `boost :: has_trivial_assign` と `std :: is_trivially_copy_assignable` は、相互に置き換えが可能であることになる。
