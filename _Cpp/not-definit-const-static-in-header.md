---
layout : post
title  : const static なメンバ変数の実装はヘッダファイルで行わない
date   : 2016/09/13
lastchange : 2017-03-05 23:53:45.
tags   :
  - C++
  - const
  - static
---


const staticなメンバ変数の実装をヘッダファイルで行うと、
**duplicate symbols for architecture**
って怒られることがある。複数のソースがconst staticなメンバ変数を実装しているヘッダをincludeしていると起こる。

```
A.cpp            -> A.o (with const static member )

    ↓ include

conststatic.h

　　( const static member )

　　↑ include

B.cpp           -> B.o (with const static member)
```

こうなった時に、A.oにもB.oにもconst static memberの実体が存在していて重複が起こり、リンクエラーが発生する。
