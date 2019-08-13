---
layout : post
title  : "エラー処理: 今までの方法"
date   : 2019/08/13
lastchange : 2019-08-14 02:24:23.
tags   :
  - C++
  - C++11
  - Error
  - try
  - catch
---

## エラー処理をまとめようと思った

どういう時に何を使ったらいいのかイマイチよくわからなかったので。

参考:

[もう少し例外を使用しても良いのではないか... - Qiita](https://qiita.com/MasayaMizuhara/items/98c0d490f1633d9b636f)


## レガシーな記述方法

C とかでよくみるエラー処理の方法は以下

```cpp
enum ErrorCode : int { 
    NONE
  , GENERIC
  , UNKNOWN
};

const ErrorCode fx( void ) { 
  return NONE;
}

void fx2( ErrorCode& code ){ 
  code = GENERIC;
}



int main( void ) {

  if( fx() != NONE ) { 
    std :: cout << "Error!" << std :: endl;
    return 1;
  }

  ErrorCode err;
  fx2( err );
  if( err != NONE ) { 
    std :: cout << "Error!" <<  std :: endl;
    return 1;
  }

  return 0;
}

```

簡単なコード解説

```cpp
enum ErrorCode : int { 
    NONE
  , GENERIC
  , UNKNOWN
};
```

エラーコードを返す簡易的な列挙型

```cpp
const ErrorCode fx( void ) { 
  return NONE;
}
```

返り値を `ErrorCode` とする形式の関数

```cpp
void fx2( ErrorCode& code ){ 
  code = GENERIC;
}
```

こちらは引数で `ErrorCode` を返す形式の関数

それぞれの呼び出しとエラー処理は

```cpp
  if( fx() != NONE ) { 
    std :: cout << "Error!" << std :: endl;
    return 1;
  }
```

```cpp
  ErrorCode err;
  fx2( err );
  if( err != NONE ) { 
    std :: cout << "Error!" <<  std :: endl;
    return 1;
  }
```

このようになっている。実行結果はこちら

```
Error!
Program ended with exit code: 1
```

今まで通りの方法は、





{% capture text %}

エラーコード(関数戻り値)、または `errno` などのグローバル変数によりエラーを通知する。しかしこれらの手法には下記理由によりうまく機能しない場合があり得る。

* エラーコードによる通知では、コンストラクタで発生したエラーを通知できない。また、戻り値をチェックしないプログラマもいる(私は `printf` の戻り値をチェックするプログラマを見たことがない)。
* `errno` などのグローバル変数を使用した通知では、マルチスレッド環境では確実にエラーを検出できない可能性がある(別スレッドがグローバル変数を書き換えてしまう可能性があるため)。また、 呼び出し側で常に `errno` を初期化する手間が発生する。

---

汎用的にエラーを扱う仕組みが無いため、上に挙げた方法を非体系的に組み合わせることになる。
その組み合わせ方には大した根拠の無いことが多く、組み合わせ方はプログラマにより異なったものになり得る。
その結果、一貫性の無い、汚いコードができあがる。

---

コードのいたるところにエラーをチェックする処理が追加される
(各関数の冒頭でのパラメータチェックや、関数の戻り値チェックなど)。
こういったチェックはエラーが発生していない場合でも実行され、当然ながら実行にはコストがかかる。

{% endcapture %}
{% capture source %}
[もう少し例外を使用しても良いのではないか... - Qiita](https://qiita.com/MasayaMizuhara/items/98c0d490f1633d9b636f)
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}






という欠点がある。上で引用させてもらったページではいくつかサンプルコードも載っている。
C++ では例外処理にいくつか選択肢が増えている。だが一方で増えた分落とし穴も増えているのでそれらを確かめながら例外処理の選択肢を増やしていく。

