---
layout : post
title  : homebrew + pyenv + anaconda + local 環境で Python3
date   : 2019/12/01
lastchange : 2019-12-01 04:27:58.
tags   :
  - Python
  - homebrew
  - pyenv
  - anaconda3
  - pyenv local
---

## global を Python3 にしない

`pyenv local` を用いて特定ディレクトリ内だけ anaconda3 と Python3 を使う。


## pyenv のインストール

[homebrew : Python3 をアンインストールして pyenv からインストールし直す]({{site.github.url}}{% link _docs/Mac/homebrew-pyenv.md %})
を参考に `pyenv` のインストール、及び _.bash\_profile_ の編集を行う。

## anaconda3-2019.10 のインストール

最新の anaconda3 を探す

```sh
$ pyenv install -l
Available versions:
  2.1.3
---- 略 ----
  3.8-dev
  3.9-dev
  activepython-2.7.14
  activepython-3.5.4
  activepython-3.6.0
  anaconda-1.4.0
  anaconda-1.5.0
  anaconda-1.5.1
  anaconda-1.6.0
  anaconda-1.6.1
  anaconda-1.7.0
  anaconda-1.8.0
  anaconda-1.9.0
  anaconda-1.9.1
  anaconda-1.9.2
  anaconda-2.0.0
  anaconda-2.0.1
  anaconda-2.1.0
  anaconda-2.2.0
  anaconda-2.3.0
  anaconda-2.4.0
  anaconda-4.0.0
  anaconda2-2.4.0
  anaconda2-2.4.1
  anaconda2-2.5.0
  anaconda2-4.0.0
  anaconda2-4.1.0
  anaconda2-4.1.1
  anaconda2-4.2.0
  anaconda2-4.3.0
  anaconda2-4.3.1
  anaconda2-4.4.0
  anaconda2-5.0.0
  anaconda2-5.0.1
  anaconda2-5.1.0
  anaconda2-5.2.0
  anaconda2-5.3.0
  anaconda2-5.3.1
  anaconda2-2018.12
  anaconda2-2019.03
  anaconda2-2019.07
  anaconda3-2.0.0
  anaconda3-2.0.1
  anaconda3-2.1.0
  anaconda3-2.2.0
  anaconda3-2.3.0
  anaconda3-2.4.0
  anaconda3-2.4.1
  anaconda3-2.5.0
  anaconda3-4.0.0
  anaconda3-4.1.0
  anaconda3-4.1.1
  anaconda3-4.2.0
  anaconda3-4.3.0
  anaconda3-4.3.1
  anaconda3-4.4.0
  anaconda3-5.0.0
  anaconda3-5.0.1
  anaconda3-5.1.0
  anaconda3-5.2.0
  anaconda3-5.3.0
  anaconda3-5.3.1
  anaconda3-2018.12
  anaconda3-2019.03
  anaconda3-2019.07
  anaconda3-2019.10
  ironpython-dev
  ironpython-2.7.4
---- 略 ----
  stackless-3.5.4
```

**anaconda3-2019.10** っぽいのでこれを入れる。

```sh
$ pyenv install anaconda3-2019.10
Downloading Anaconda3-2019.10-MacOSX-x86_64.sh...
-> https://repo.continuum.io/archive/Anaconda3-2019.10-MacOSX-x86_64.sh
Installing Anaconda3-2019.10-MacOSX-x86_64...
Installed Anaconda3-2019.10-MacOSX-x86_64 to /Applications/Homebrew/opt/pyenv/Python/versions/anaconda3-2019.10
```

## ディレクトリ適用設定

実行したいディレクトリでバージョンの設定を行う。

```sh
$ pyenv local anaconda3-2019.10
```

確認を行う。

```sh
$ pyenv exec python --version
Python 3.7.4
```

`pyenv exec` を省略すると `global` が参照されてしまうので注意。

```sh
$ python --version
Python 2.7.10
```

