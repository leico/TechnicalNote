---
layout : post
title  : homebrew + pyenv + venv + tensorflow + keras な環境構築
date   : 2020/02/17
lastchange : 2020-02-17 11:34:57.
tags   :
  - Python
  - homebrew
  - pyenv
  - anaconda3
  - pyenv local
---

## バージョン指定しながら各種をインストールする

Python 関係が環境構築記事ばかりだが、利用環境が多様すぎるのでしょうがない。
今回はバージョンを指定しながら keras や tensorflow を導入する。
加えて、限られた環境にだけパッケージのインストールを許すことも `venv` を使ってやってみる。

参考:

* [pyenv、pyenv-virtualenv、venv、Anaconda、Pipenv。私はPipenvを使う。 - Qiita](https://qiita.com/KRiver1/items/c1788e616b77a9bad4dd)
* [【Python入門】venvで仮想環境を作る方法をわかりやすく解説 \| 侍エンジニア塾ブログ（Samurai Blog） - プログラミング入門者向けサイト](https://www.sejuku.net/blog/68423)
* [pyenv + venvでPython3環境を構築する - \$shibayu36->blog;](https://blog.shibayu36.org/entry/2017/03/31/223540)
* [Tensorflowのバージョンを指定してインストールする方法 \| 技術的特異点](http://tecsingularity.com/cuda/version/)
* [Install TensorFlow with pip  \|  TensorFlow](https://www.tensorflow.org/install/pip?hl=Language&lang=python3#older-versions-of-tensorflow)
* [How can I install keras with older version? Not 2.0.1 version · Issue #5852 · keras-team/keras](https://github.com/keras-team/keras/issues/5852)
* [データ分析で欠かせない！Jupyter Notebookの使い方【初心者向け】 \| TechAcademyマガジン](https://techacademy.jp/magazine/17430)




{% capture text %}

`Python` インタプリタには、様々なバージョンがある。
もしあなたが考古学者なら、 `Python2` 系の存在を知っていることだろう。
3系の中でも互換性のない変更はたくさん行われているし、
あるいは`PyPy` や `IronPython` といった（よく知られた）`Python`
とは異なる実装のインタプリタもある。
つまり、我々はたくさんの `Python` を持っていて、
それらをプロジェクトに応じて適切に切り替えなければならない。

また、`Python` には様々なパッケージがある。
開発者はこれらを好き勝手にインストールして使うわけだが、
もちろんこれらのパッケージにもバージョンや互換性の問題は存在する。
「このプロジェクトは、このパッケージをインストールしないと動きません。
ただし、バージョンxx以上yy以下です。」
という注意書きがすべてのプロジェクトに存在しており、
それらをうまく扱うためにはパッケージのバージョンを切り替えてくれるツールが当然必要だ。

そういうわけで、我々は2つのものごとについて仮想環境を必要としている。
すなわち、`Python` インタプリタ本体と、パッケージ群である。

## まとめ

|                   | インタプリタ切替 | パッケージ切替 | パッケージインストール |
|-------------------|:----------------:|:--------------:|:----------------------:|
| pyenv             | ○                |                |                        |
| virtualenv        | ○                | ○              |                        |
| pip               |                  |                | ○                      |
| venv              |                  | ○              |                        |
| pyenv-virtualenv  | ○                | ○              |                        |
| virtualenvwrapper |                  | ○              |                        |
| Anaconda          | ○                | ○              | △                      |
| Pipenv            |                  | ○              | ◎                      |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[pyenv、pyenv-virtualenv、venv、Anaconda、Pipenv。私はPipenvを使う。 - Qiita](https://qiita.com/KRiver1/items/c1788e616b77a9bad4dd)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


仕方がないのだけど、この時点ですでに厄介だ。
引用元は pipenv を利用しているが、ひとまず公式の `venv` を使うことにした。


## ローカル Python のバージョンを設定する

```sh
$ pyenv install 3.7.3
```

してから、

```sh
$ pyenv versions
* system (set by /Applications/Homebrew/opt/pyenv/Python/version)
  3.7.3
```

確認して

```sh
$ pyenv local 3.7.3
$ pyenv versions
  system
* 3.7.3 (set by /Users/leico_studio/Project/Shiseido/LSTM/.python-version)
```

設定して、確認。

詳細は [homebrew : Python3 をアンインストールして pyenv からインストールし直す]({{site.github.url}}{% link _docs/Mac/homebrew-pyenv.md %}) 。





## `venv` で仮想環境を構築して activate する




{% capture text %}

### 仮想環境の作成

それではまずはじめに仮想環境を作りましょう。こちらのコマンドをご覧ください。

```sh
python3 -m venv 環境の名前
```

このコマンドを実行させることで、仮想環境が作られます。
しかしこの仮想環境は作ったディレクトリに入るだけでは機能しません。
仮想環境に入るときはactivate（有効化）、
仮想環境から出るときはdeactivate（無効化）という作業が必要になります。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[【Python入門】venvで仮想環境を作る方法をわかりやすく解説 \| 侍エンジニア塾ブログ（Samurai Blog） - プログラミング入門者向けサイト](https://www.sejuku.net/blog/68423)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}







{% capture text %}


#### 環境の用意

まずTensorFlowを試すことを見越して、 `tensorflow-playground` というディレクトリで環境を作ってみる。

```sh
$ mkdir tensorflow-playground
$ cd tensorflow-playground
$ python3.6 -m venv .
```

これでこのディレクトリ配下に `bin` や`include` などのディレクトリが出来たので、用意完了。
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[pyenv + venvでPython3環境を構築する - \$shibayu36->blog;](https://blog.shibayu36.org/entry/2017/03/31/223540)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`環境の名前` を `.` にすることで、環境のフォルダ名になるようだった。
これで作成した。




## 環境の有効化/無効化



{% capture text %}


#### 環境に入る

それでこの環境で作業したいときは、以下のコマンドを実行する。

```sh
$ source bin/activate
```

するとプロンプトに以下のように環境名が表示される。
これでこの環境内で作業が出来るようになる。

```sh
(tensorflow-playground) $
```

#### 環境から出る

deactivateすれば出れる。

```sh
$ deactivate
```

プロンプトから`(tensorflow-playground)` の表示が消えたらOK。


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[pyenv + venvでPython3環境を構築する - \$shibayu36->blog;](https://blog.shibayu36.org/entry/2017/03/31/223540)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


この記述の通り。





## 仮想環境に TensorFlow , Keras をインストールする

TensorFlow 、 Keras 共にバージョン指定をして導入する。




{% capture text %}

**「pip install tensorflow-gpu==1.3.0」**

とバージョンを指定してやればOKです。

もう一度「pip list」すると、確かにバージョンが変わったことがわかります。


## まとめ

1. Tensorflowのバージョンを確認したい。
    ```sh
    pip list
    ```

2. バージョンを指定してTensorflowをインストールしたい(GPU版)。
    ```sh
    pip install tensorflow-gpu==【バージョン番号】
    ```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Tensorflowのバージョンを指定してインストールする方法 \| 技術的特異点](http://tecsingularity.com/cuda/version/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% capture text %}

### Older versions of TensorFlow

For TensorFlow 1.x, CPU and GPU packages are separate:

`tensorflow==1.15` 
: —Release for CPU-only

`tensorflow-gpu==1.15`
: —Release with [GPU support](https://www.tensorflow.org/install/gpu?hl=Language)
 (Ubuntu and Windows)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[Install TensorFlow with pip  \|  TensorFlow](https://www.tensorflow.org/install/pip?hl=Language&lang=python3#older-versions-of-tensorflow)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`packageName==version` という記述で指定したバージョンでのインストールができるようだ。


{% capture text %}

##### [keunwoochoi](https://github.com/keunwoochoi) commented on 20 Mar 2017

`$ pip install keras==1.2`

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[How can I install keras with older version? Not 2.0.1 version · Issue #5852 · keras-team/keras](https://github.com/keras-team/keras/issues/5852)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

こちらも同様らしい。

まずは TensorFlow 1.14.0 をインストール。

```sh
$ pip install tensorflow==1.14.0
Collecting tensorflow==1.14.0
  Downloading https://files.pythonhosted.org/packages/ed/11/037887c5cbac5af3124050fb6348e67caa038734cc9673b11c31c8939072/tensorflow-1.14.0-cp37-cp37m-macosx_10_11_x86_64.whl (105.8MB)
    100% |████████████████████████████████| 105.8MB 489kB/s 
Collecting wheel>=0.26 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/8c/23/848298cccf8e40f5bbb59009b32848a4c38f4e7f3364297ab3c3e2e2cd14/wheel-0.34.2-py2.py3-none-any.whl
Collecting google-pasta>=0.1.6 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/c3/fd/1e86bc4837cc9a3a5faf3db9b1854aa04ad35b5f381f9648fbe81a6f94e4/google_pasta-0.1.8-py3-none-any.whl (57kB)
    100% |████████████████████████████████| 61kB 12.5MB/s 
Collecting tensorflow-estimator<1.15.0rc0,>=1.14.0rc0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/3c/d5/21860a5b11caf0678fbc8319341b0ae21a07156911132e0e71bffed0510d/tensorflow_estimator-1.14.0-py2.py3-none-any.whl (488kB)
    100% |████████████████████████████████| 491kB 8.1MB/s 
Collecting tensorboard<1.15.0,>=1.14.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/91/2d/2ed263449a078cd9c8a9ba50ebd50123adf1f8cfbea1492f9084169b89d9/tensorboard-1.14.0-py3-none-any.whl (3.1MB)
    100% |████████████████████████████████| 3.2MB 3.3MB/s 
Collecting six>=1.10.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/65/eb/1f97cb97bfc2390a276969c6fae16075da282f5058082d4cb10c6c5c1dba/six-1.14.0-py2.py3-none-any.whl
Collecting wrapt>=1.11.1 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/23/84/323c2415280bc4fc880ac5050dddfb3c8062c2552b34c2e512eb4aa68f79/wrapt-1.11.2.tar.gz
Collecting grpcio>=1.8.6 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/35/f5/9dce584256802e9d26f51e689bf42b365442c6c52f1af8a44c86ad62359a/grpcio-1.27.2-cp37-cp37m-macosx_10_9_x86_64.whl (2.5MB)
    100% |████████████████████████████████| 2.5MB 6.4MB/s 
Collecting numpy<2.0,>=1.14.5 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/2f/5b/2cc2b9285e8b2ca8d2c1e4a2cbf1b12d70a2488ea78170de1909bca725f2/numpy-1.18.1-cp37-cp37m-macosx_10_9_x86_64.whl (15.1MB)
    100% |████████████████████████████████| 15.1MB 2.1MB/s 
Collecting astor>=0.6.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/c3/88/97eef84f48fa04fbd6750e62dcceafba6c63c81b7ac1420856c8dcc0a3f9/astor-0.8.1-py2.py3-none-any.whl
Collecting absl-py>=0.7.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/1a/53/9243c600e047bd4c3df9e69cfabc1e8004a82cac2e0c484580a78a94ba2a/absl-py-0.9.0.tar.gz (104kB)
    100% |████████████████████████████████| 112kB 10.7MB/s 
Collecting termcolor>=1.1.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/8a/48/a76be51647d0eb9f10e2a4511bf3ffb8cc1e6b14e9e4fab46173aa79f981/termcolor-1.1.0.tar.gz
Collecting keras-applications>=1.0.6 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/71/e3/19762fdfc62877ae9102edf6342d71b28fbfd9dea3d2f96a882ce099b03f/Keras_Applications-1.0.8-py3-none-any.whl (50kB)
    100% |████████████████████████████████| 51kB 15.9MB/s 
Collecting protobuf>=3.6.1 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/4c/25/c057a298635d08d087a20f51ff4287d821814208ebb045d84ea65535b3e3/protobuf-3.11.3-cp37-cp37m-macosx_10_9_x86_64.whl (1.3MB)
    100% |████████████████████████████████| 1.3MB 4.3MB/s 
Collecting keras-preprocessing>=1.0.5 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/28/6a/8c1f62c37212d9fc441a7e26736df51ce6f0e38455816445471f10da4f0a/Keras_Preprocessing-1.1.0-py2.py3-none-any.whl (41kB)
    100% |████████████████████████████████| 51kB 19.9MB/s 
Collecting gast>=0.2.0 (from tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/d6/84/759f5dd23fec8ba71952d97bcc7e2c9d7d63bdc582421f3cd4be845f0c98/gast-0.3.3-py2.py3-none-any.whl
Collecting setuptools>=41.0.0 (from tensorboard<1.15.0,>=1.14.0->tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/3d/72/1c1498c1e908e0562b1e1cd30012580baa7d33b5b0ffdbeb5fde2462cc71/setuptools-45.2.0-py3-none-any.whl (584kB)
    100% |████████████████████████████████| 593kB 11.1MB/s 
Collecting werkzeug>=0.11.15 (from tensorboard<1.15.0,>=1.14.0->tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/ba/a5/d6f8a6e71f15364d35678a4ec8a0186f980b3bd2545f40ad51dd26a87fb1/Werkzeug-1.0.0-py2.py3-none-any.whl (298kB)
    100% |████████████████████████████████| 307kB 5.0MB/s 
Collecting markdown>=2.6.8 (from tensorboard<1.15.0,>=1.14.0->tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/ab/c4/ba46d44855e6eb1770a12edace5a165a0c6de13349f592b9036257f3c3d3/Markdown-3.2.1-py2.py3-none-any.whl (88kB)
    100% |████████████████████████████████| 92kB 15.9MB/s 
Collecting h5py (from keras-applications>=1.0.6->tensorflow==1.14.0)
  Downloading https://files.pythonhosted.org/packages/1a/8b/4d01ae9a9d50a0bcc7b0b9aae41785d8d9de6fa9bba04dc20b1582181d2d/h5py-2.10.0-cp37-cp37m-macosx_10_6_intel.whl (3.0MB)
    100% |████████████████████████████████| 3.0MB 4.9MB/s 
Installing collected packages: wheel, six, google-pasta, tensorflow-estimator, grpcio, setuptools, protobuf, numpy, absl-py, werkzeug, markdown, tensorboard, wrapt, astor, termcolor, h5py, keras-applications, keras-preprocessing, gast, tensorflow
  Found existing installation: setuptools 40.8.0
    Uninstalling setuptools-40.8.0:
      Successfully uninstalled setuptools-40.8.0
  Running setup.py install for absl-py ... done
  Running setup.py install for wrapt ... done
  Running setup.py install for termcolor ... done
Successfully installed absl-py-0.9.0 astor-0.8.1 gast-0.3.3 google-pasta-0.1.8 grpcio-1.27.2 h5py-2.10.0 keras-applications-1.0.8 keras-preprocessing-1.1.0 markdown-3.2.1 numpy-1.18.1 protobuf-3.11.3 setuptools-45.2.0 six-1.14.0 tensorboard-1.14.0 tensorflow-1.14.0 tensorflow-estimator-1.14.0 termcolor-1.1.0 werkzeug-1.0.0 wheel-0.34.2 wrapt-1.11.2
```



次に、 Keras 2.2.4 をインストール。




```sh
$ pip install keras==2.2.4
Collecting keras==2.2.4
  Downloading https://files.pythonhosted.org/packages/5e/10/aa32dad071ce52b5502266b5c659451cfd6ffcbf14e6c8c4f16c0ff5aaab/Keras-2.2.4-py2.py3-none-any.whl (312kB)
    100% |████████████████████████████████| 317kB 4.2MB/s 
Requirement already satisfied: keras-preprocessing>=1.0.5 in ./lib/python3.7/site-packages (from keras==2.2.4) (1.1.0)
Collecting scipy>=0.14 (from keras==2.2.4)
  Downloading https://files.pythonhosted.org/packages/85/7a/ae480be23b768910a9327c33517ced4623ba88dc035f9ce0206657c353a9/scipy-1.4.1-cp37-cp37m-macosx_10_6_intel.whl (28.4MB)
    100% |████████████████████████████████| 28.4MB 1.3MB/s 
Requirement already satisfied: six>=1.9.0 in ./lib/python3.7/site-packages (from keras==2.2.4) (1.14.0)
Requirement already satisfied: h5py in ./lib/python3.7/site-packages (from keras==2.2.4) (2.10.0)
Collecting pyyaml (from keras==2.2.4)
  Downloading https://files.pythonhosted.org/packages/3d/d9/ea9816aea31beeadccd03f1f8b625ecf8f645bd66744484d162d84803ce5/PyYAML-5.3.tar.gz (268kB)
    100% |████████████████████████████████| 276kB 5.1MB/s 
Requirement already satisfied: numpy>=1.9.1 in ./lib/python3.7/site-packages (from keras==2.2.4) (1.18.1)
Requirement already satisfied: keras-applications>=1.0.6 in ./lib/python3.7/site-packages (from keras==2.2.4) (1.0.8)
Building wheels for collected packages: pyyaml
  Building wheel for pyyaml (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/e4/76/4d/a95b8dd7b452b69e8ed4f68b69e1b55e12c9c9624dd962b191
Successfully built pyyaml
Installing collected packages: scipy, pyyaml, keras
Successfully installed keras-2.2.4 pyyaml-5.3 scipy-1.4.1
```


インストール完了。

## Jupyter をインストールする

{% capture text %}

## Jupyter Notebookとは

Jupyter Notebookは、ノートブックと呼ばれるファイルにプログラムや説明の文章、
実行結果などをまとめて管理できる、データ分析用のツールです。
「ジュパイター ノートブック」または「ジュピター ノートブック」と読みます。

Jupyter Notebookはオープンソースで公開されており、個人／商用問わず、
誰でも無料で利用することができます。
またブラウザで動作するため、いろいろなOSから利用でき、
チームメンバーとのプログラムの共有も用意です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[データ分析で欠かせない！Jupyter Notebookの使い方【初心者向け】 \| TechAcademyマガジン](https://techacademy.jp/magazine/17430)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


これも必要になったのでインストールする。

```sh
$ pip install Jupyter
Collecting Jupyter
  Downloading https://files.pythonhosted.org/packages/83/df/0f5dd132200728a86190397e1ea87cd76244e42d39ec5e88efd25b2abd7e/jupyter-1.0.0-py2.py3-none-any.whl
Collecting ipywidgets (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/56/a0/dbcf5881bb2f51e8db678211907f16ea0a182b232c591a6d6f276985ca95/ipywidgets-7.5.1-py2.py3-none-any.whl (121kB)
    100% |████████████████████████████████| 122kB 2.4MB/s 
Collecting qtconsole (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/7c/57/3528b84ffa753e2089908bbf74bb5ae60653eb7a63797b6234e88b847d67/qtconsole-4.6.0-py2.py3-none-any.whl (121kB)
    100% |████████████████████████████████| 122kB 3.5MB/s 
Collecting ipykernel (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/d7/62/d1a5d654b7a21bd3eb99be1b59a608cc18a7a08ed88495457a87c40a0495/ipykernel-5.1.4-py3-none-any.whl (116kB)
    100% |████████████████████████████████| 122kB 2.8MB/s 
Collecting notebook (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/b1/f1/0a67f09ef53a342403ffa66646ee39273e0ac79ffa5de5dbe2f3e28b5bdf/notebook-6.0.3-py3-none-any.whl (9.7MB)
    100% |████████████████████████████████| 9.7MB 3.5MB/s 
Collecting jupyter-console (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/0a/89/742fa5a80b552ffcb6a8922712697c6e6828aee7b91ee4ae2b79f00f8401/jupyter_console-6.1.0-py2.py3-none-any.whl
Collecting nbconvert (from Jupyter)
  Downloading https://files.pythonhosted.org/packages/79/6c/05a569e9f703d18aacb89b7ad6075b404e8a4afde2c26b73ca77bb644b14/nbconvert-5.6.1-py2.py3-none-any.whl (455kB)
    100% |████████████████████████████████| 460kB 11.1MB/s 
Collecting traitlets>=4.3.1 (from ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ca/ab/872a23e29cec3cf2594af7e857f18b687ad21039c1f9b922fac5b9b142d5/traitlets-4.3.3-py2.py3-none-any.whl (75kB)
    100% |████████████████████████████████| 81kB 12.7MB/s 
Collecting widgetsnbextension~=3.5.0 (from ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/6c/7b/7ac231c20d2d33c445eaacf8a433f4e22c60677eb9776c7c5262d7ddee2d/widgetsnbextension-3.5.1-py2.py3-none-any.whl (2.2MB)
    100% |████████████████████████████████| 2.2MB 8.6MB/s 
Collecting nbformat>=4.2.0 (from ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ac/eb/de575b7a64e7ab8d8c95e4c180ccc36deda3f1379186c4ee7adf6c2f1586/nbformat-5.0.4-py3-none-any.whl (169kB)
    100% |████████████████████████████████| 174kB 9.0MB/s 
Collecting ipython>=4.0.0; python_version >= "3.3" (from ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/b8/6d/1e3e335e767fc15a2047a008e27df31aa8bcf11c6f3805d03abefc69aa88/ipython-7.12.0-py3-none-any.whl (777kB)
    100% |████████████████████████████████| 778kB 8.3MB/s 
Collecting ipython-genutils (from qtconsole->Jupyter)
  Downloading https://files.pythonhosted.org/packages/fa/bc/9bd3b5c2b4774d5f33b2d544f1460be9df7df2fe42f352135381c347c69a/ipython_genutils-0.2.0-py2.py3-none-any.whl
Collecting jupyter-client>=4.1 (from qtconsole->Jupyter)
  Downloading https://files.pythonhosted.org/packages/13/81/fe0eee1bcf949851a120254b1f530ae1e01bdde2d3ab9710c6ff81525061/jupyter_client-5.3.4-py2.py3-none-any.whl (92kB)
    100% |████████████████████████████████| 92kB 12.4MB/s 
Collecting jupyter-core (from qtconsole->Jupyter)
  Downloading https://files.pythonhosted.org/packages/7b/c0/ae033e3c01889bc792d062a954b840e0de21e3ca16b23fa203b7a09b2aae/jupyter_core-4.6.2-py2.py3-none-any.whl (83kB)
    100% |████████████████████████████████| 92kB 16.0MB/s 
Collecting pygments (from qtconsole->Jupyter)
  Downloading https://files.pythonhosted.org/packages/be/39/32da3184734730c0e4d3fa3b2b5872104668ad6dc1b5a73d8e477e5fe967/Pygments-2.5.2-py2.py3-none-any.whl (896kB)
    100% |████████████████████████████████| 901kB 7.8MB/s 
Collecting appnope; platform_system == "Darwin" (from ipykernel->Jupyter)
  Downloading https://files.pythonhosted.org/packages/87/a9/7985e6a53402f294c8f0e8eff3151a83f1fb901fa92909bb3ff29b4d22af/appnope-0.1.0-py2.py3-none-any.whl
Collecting tornado>=4.2 (from ipykernel->Jupyter)
  Downloading https://files.pythonhosted.org/packages/30/78/2d2823598496127b21423baffaa186b668f73cd91887fcef78b6eade136b/tornado-6.0.3.tar.gz (482kB)
    100% |████████████████████████████████| 491kB 12.2MB/s 
Collecting pyzmq>=17 (from notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/71/0e/ee7685d1340a4cc29fb64d164b5c5667122b2ec1a94cfc923adeef378116/pyzmq-18.1.1-cp37-cp37m-macosx_10_9_x86_64.whl (811kB)
    100% |████████████████████████████████| 819kB 11.3MB/s 
Collecting jinja2 (from notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/27/24/4f35961e5c669e96f6559760042a55b9bcfcdb82b9bdb3c8753dbe042e35/Jinja2-2.11.1-py2.py3-none-any.whl (126kB)
    100% |████████████████████████████████| 133kB 10.7MB/s 
Collecting Send2Trash (from notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/49/46/c3dc27481d1cc57b9385aff41c474ceb7714f7935b1247194adae45db714/Send2Trash-1.5.0-py3-none-any.whl
Collecting terminado>=0.8.1 (from notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ff/96/1d9a2c23990aea8f8e0b5c3b6627d03196a73771a17a2d9860bbe9823ab6/terminado-0.8.3-py2.py3-none-any.whl
Collecting prometheus-client (from notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/b3/23/41a5a24b502d35a4ad50a5bb7202a5e1d9a0364d0c12f56db3dbf7aca76d/prometheus_client-0.7.1.tar.gz
Collecting prompt-toolkit!=3.0.0,!=3.0.1,<3.1.0,>=2.0.0 (from jupyter-console->Jupyter)
  Downloading https://files.pythonhosted.org/packages/f5/22/f00412fafc68169054cc623a35c32773f22b403ddbe516c8adfdecf25341/prompt_toolkit-3.0.3-py3-none-any.whl (348kB)
    100% |████████████████████████████████| 358kB 6.8MB/s 
Collecting mistune<2,>=0.8.1 (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/09/ec/4b43dae793655b7d8a25f76119624350b4d65eb663459eb9603d7f1f0345/mistune-0.8.4-py2.py3-none-any.whl
Collecting bleach (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ab/05/27e1466475e816d3001efb6e0a85a819be17411420494a1e602c36f8299d/bleach-3.1.0-py2.py3-none-any.whl (157kB)
    100% |████████████████████████████████| 163kB 8.6MB/s 
Collecting pandocfilters>=1.4.1 (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/4c/ea/236e2584af67bb6df960832731a6e5325fd4441de001767da328c33368ce/pandocfilters-1.4.2.tar.gz
Collecting testpath (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/1b/9e/1a170feaa54f22aeb5a5d16c9015e82234275a3c8ab630b552493f9cb8a9/testpath-0.4.4-py2.py3-none-any.whl (163kB)
    100% |████████████████████████████████| 174kB 12.5MB/s 
Collecting defusedxml (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/06/74/9b387472866358ebc08732de3da6dc48e44b0aacd2ddaa5cb85ab7e986a2/defusedxml-0.6.0-py2.py3-none-any.whl
Collecting entrypoints>=0.2.2 (from nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ac/c6/44694103f8c221443ee6b0041f69e2740d89a25641e62fb4f2ee568f2f9c/entrypoints-0.3-py2.py3-none-any.whl
Collecting decorator (from traitlets>=4.3.1->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/8f/b7/f329cfdc75f3d28d12c65980e4469e2fa373f1953f5df6e370e84ea2e875/decorator-4.4.1-py2.py3-none-any.whl
Requirement already satisfied: six in ./lib/python3.7/site-packages (from traitlets>=4.3.1->ipywidgets->Jupyter) (1.14.0)
Collecting jsonschema!=2.5.0,>=2.4 (from nbformat>=4.2.0->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/c5/8f/51e89ce52a085483359217bc72cdbf6e75ee595d5b1d4b5ade40c7e018b8/jsonschema-3.2.0-py2.py3-none-any.whl (56kB)
    100% |████████████████████████████████| 61kB 18.7MB/s 
Requirement already satisfied: setuptools>=18.5 in ./lib/python3.7/site-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter) (45.2.0)
Collecting pickleshare (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/9a/41/220f49aaea88bc6fa6cba8d05ecf24676326156c23b991e80b3f2fc24c77/pickleshare-0.7.5-py2.py3-none-any.whl
Collecting jedi>=0.10 (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/01/67/333e2196b70840f411fd819407b4e98aa3150c2bd24c52154a451f912ef2/jedi-0.16.0-py2.py3-none-any.whl (1.1MB)
    100% |████████████████████████████████| 1.1MB 277kB/s 
Collecting pexpect; sys_platform != "win32" (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/39/7b/88dbb785881c28a102619d46423cb853b46dbccc70d3ac362d99773a78ce/pexpect-4.8.0-py2.py3-none-any.whl (59kB)
    100% |████████████████████████████████| 61kB 11.3MB/s 
Collecting backcall (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/84/71/c8ca4f5bb1e08401b916c68003acf0a0655df935d74d93bf3f3364b310e0/backcall-0.1.0.tar.gz
Collecting python-dateutil>=2.1 (from jupyter-client>=4.1->qtconsole->Jupyter)
  Downloading https://files.pythonhosted.org/packages/d4/70/d60450c3dd48ef87586924207ae8907090de0b306af2bce5d134d78615cb/python_dateutil-2.8.1-py2.py3-none-any.whl (227kB)
    100% |████████████████████████████████| 235kB 12.4MB/s 
Collecting MarkupSafe>=0.23 (from jinja2->notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ce/c6/f000f1af136ef74e4a95e33785921c73595c5390403f102e9b231b065b7a/MarkupSafe-1.1.1-cp37-cp37m-macosx_10_6_intel.whl
Collecting ptyprocess; os_name != "nt" (from terminado>=0.8.1->notebook->Jupyter)
  Downloading https://files.pythonhosted.org/packages/d1/29/605c2cc68a9992d18dada28206eeada56ea4bd07a239669da41674648b6f/ptyprocess-0.6.0-py2.py3-none-any.whl
Collecting wcwidth (from prompt-toolkit!=3.0.0,!=3.0.1,<3.1.0,>=2.0.0->jupyter-console->Jupyter)
  Downloading https://files.pythonhosted.org/packages/58/b4/4850a0ccc6f567cc0ebe7060d20ffd4258b8210efadc259da62dc6ed9c65/wcwidth-0.1.8-py2.py3-none-any.whl
Collecting webencodings (from bleach->nbconvert->Jupyter)
  Downloading https://files.pythonhosted.org/packages/f4/24/2a3e3df732393fed8b3ebf2ec078f05546de641fe1b667ee316ec1dcf3b7/webencodings-0.5.1-py2.py3-none-any.whl
Collecting importlib-metadata; python_version < "3.8" (from jsonschema!=2.5.0,>=2.4->nbformat>=4.2.0->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/8b/03/a00d504808808912751e64ccf414be53c29cad620e3de2421135fcae3025/importlib_metadata-1.5.0-py2.py3-none-any.whl
Collecting attrs>=17.4.0 (from jsonschema!=2.5.0,>=2.4->nbformat>=4.2.0->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/a2/db/4313ab3be961f7a763066401fb77f7748373b6094076ae2bda2806988af6/attrs-19.3.0-py2.py3-none-any.whl
Collecting pyrsistent>=0.14.0 (from jsonschema!=2.5.0,>=2.4->nbformat>=4.2.0->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/90/aa/cdcf7ef88cc0f831b6f14c8c57318824c9de9913fe8de38e46a98c069a35/pyrsistent-0.15.7.tar.gz (107kB)
    100% |████████████████████████████████| 112kB 9.6MB/s 
Collecting parso>=0.5.2 (from jedi>=0.10->ipython>=4.0.0; python_version >= "3.3"->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/ec/bb/3b6c9f604ac40e2a7833bc767bd084035f12febcbd2b62204c5bc30edf97/parso-0.6.1-py2.py3-none-any.whl (97kB)
    100% |████████████████████████████████| 102kB 7.9MB/s 
Collecting zipp>=0.5 (from importlib-metadata; python_version < "3.8"->jsonschema!=2.5.0,>=2.4->nbformat>=4.2.0->ipywidgets->Jupyter)
  Downloading https://files.pythonhosted.org/packages/46/42/f2dd964b2a6b1921b08d661138148c1bcd3f038462a44019416f2342b618/zipp-2.2.0-py36-none-any.whl
Building wheels for collected packages: tornado, prometheus-client, pandocfilters, backcall, pyrsistent
  Building wheel for tornado (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/84/bf/40/2f6ef700f48401ca40e5e3dd7d0e3c0a90e064897b7fe5fc08
  Building wheel for prometheus-client (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/1c/54/34/fd47cd9b308826cc4292b54449c1899a30251ef3b506bc91ea
  Building wheel for pandocfilters (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/39/01/56/f1b08a6275acc59e846fa4c1e1b65dbc1919f20157d9e66c20
  Building wheel for backcall (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/98/b0/dd/29e28ff615af3dda4c67cab719dd51357597eabff926976b45
  Building wheel for pyrsistent (setup.py) ... done
  Stored in directory: /Users/leico_studio/Library/Caches/pip/wheels/b5/78/ac/f26a78a989cd97f90981d96a560d7e1da5e1307284301d94e8
Successfully built tornado prometheus-client pandocfilters backcall pyrsistent
Installing collected packages: decorator, pickleshare, wcwidth, prompt-toolkit, appnope, parso, jedi, ptyprocess, pexpect, pygments, ipython-genutils, traitlets, backcall, ipython, jupyter-core, pyzmq, python-dateutil, tornado, jupyter-client, ipykernel, zipp, importlib-metadata, attrs, pyrsistent, jsonschema, nbformat, MarkupSafe, jinja2, Send2Trash, terminado, mistune, webencodings, bleach, pandocfilters, testpath, defusedxml, entrypoints, nbconvert, prometheus-client, notebook, widgetsnbextension, ipywidgets, qtconsole, jupyter-console, Jupyter
Successfully installed Jupyter-1.0.0 MarkupSafe-1.1.1 Send2Trash-1.5.0 appnope-0.1.0 attrs-19.3.0 backcall-0.1.0 bleach-3.1.0 decorator-4.4.1 defusedxml-0.6.0 entrypoints-0.3 importlib-metadata-1.5.0 ipykernel-5.1.4 ipython-7.12.0 ipython-genutils-0.2.0 ipywidgets-7.5.1 jedi-0.16.0 jinja2-2.11.1 jsonschema-3.2.0 jupyter-client-5.3.4 jupyter-console-6.1.0 jupyter-core-4.6.2 mistune-0.8.4 nbconvert-5.6.1 nbformat-5.0.4 notebook-6.0.3 pandocfilters-1.4.2 parso-0.6.1 pexpect-4.8.0 pickleshare-0.7.5 prometheus-client-0.7.1 prompt-toolkit-3.0.3 ptyprocess-0.6.0 pygments-2.5.2 pyrsistent-0.15.7 python-dateutil-2.8.1 pyzmq-18.1.1 qtconsole-4.6.0 terminado-0.8.3 testpath-0.4.4 tornado-6.0.3 traitlets-4.3.3 wcwidth-0.1.8 webencodings-0.5.1 widgetsnbextension-3.5.1 zipp-2.2.0
```



完了！
