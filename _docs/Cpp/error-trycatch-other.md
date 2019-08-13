---
layout : post
title  : "エラー処理: その他見つけてしまった注意事項 "
date   : 2019/08/13
lastchange : 2019-08-14 07:18:27.
tags   :
  - C++
  - C++11
  - Error
  - try
  - catch
---

## 一連の記事を書いている中で見つけてしまったもの

この動作はどこにもかかれていないが

## `catch` 内 `throw` の問題

記事を書いている内に見つけたものなのだが、


```cpp
#include <iostream>
#include <stdexcept>


int main( void ) {

  try{ 

    try{ 
      throw std :: runtime_error( "Error!" );
    }
    catch( std :: exception& e ){ 
      throw e; //<- Here
    }
  }
  catch( std :: exception& e ){ 
    std :: cerr << e.what() << std :: endl;
  }


  return 0;
}

```

上位 `try` `catch` へ `catch` の引数を渡しているのだが、これをすると実行結果は


```
std::exception
Program ended with exit code: 0
```

最初のコンストラクタで引数に入れた文字列が `std :: exception` クラスの初期値で上書きされてしまう。
誤って渡してしまわないように注意しなければならない。



