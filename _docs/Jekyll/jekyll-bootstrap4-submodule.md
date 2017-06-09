---
layout : post
title  : Github Pagesにsubmoduleとしてbootstrapを入れる
date : 2017/06/09
lastchange : 2017-06-10 02:52:06.
tags   :
  - jekyll
  - github
  - pages
  - submodule
  - symlink
---

## bootstrapもsubmoduleにしてバージョン管理したい

そういえば[bootstrap](https://github.com/twbs/bootstrap)もgithubでバージョン管理されているので、
`git submodule`を用いてインストール/バージョン管理しながらアップデートできたら楽だな。
[MathJaxをインストールする]({{site.github.url}}{% link _docs/Jekyll/mathjax-install.md %})のように。

ついでに **submodule** たちを一つのディレクトリで一元管理したい。

参考:

* [Assets \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/assets/)
* [Accept multiple Sass load paths · Issue #3366 · jekyll/jekyll](https://github.com/jekyll/jekyll/issues/3366)
* [Configuring Jekyll - User Documentation](https://help.github.com/articles/configuring-jekyll/#configuration-settings-you-cannot-change)
* [GitHub Pages symlink test](https://s4y.github.io/gh-pages-symlink-test/)
    * [s4y/gh-pages-symlink-test](https://github.com/s4y/gh-pages-symlink-test)
* [git submoduleを今風な感じで削除する - Qiita](http://qiita.com/u1aryz/items/8d1923da79158439eeaa)
* [【Git】tag関連コマンド - Qiita](http://qiita.com/chihiro/items/cba40015b1aa2c73b78a)
* [git submodule でブランチ/タグをチェックアウトする - Qiita](http://qiita.com/kysnm/items/c083674deac0c4a34756)
* [ Getting started · Bootstrap ](https://getbootstrap.com/getting-started/#whats-included-source)


## Jekyllの仕様

> If you are using Sass `@import` statements, 
> you’ll need to ensure that your `sass_dir` is set to the base directory 
> that contains your Sass files. You can do that thusly:
>
> ---
>
> ##### The `sass_dir` is only used by Sass
> 
> Note that the `sass_dir` becomes the load path for Sass imports, nothing more.
> This means that Jekyll does not know about these files directly,
> so any files here should not contain the YAML Front Matter as described above
> nor will they be transformed as described above. This folder should only contain imports.
> 
> ---
> 
> * [Assets \| Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/assets/)

**\_sass** ディレクトリには scss/sass ファイルしか置くことができない。
さらに他の scss/sass ファイルから `@import` するファイルはすべて **\_sass** に入れておけと書いてある。

bootstrap は javascript と css(scss) で構成されている。
なので **js** または **\_sass** ディレクトリへ、単純に `git submodule` するだけでは動作しない。





## `load_paths` で複数の **\_sass** ディレクトリを指定できる

> ##### [parkr](https://github.com/parkr) commented on 27 Jan 2015
> 
> Specify them under the load_paths key.
> [It's already implemented when not in safe mode](https://github.com/jekyll/jekyll-sass-converter/blob/master/lib/jekyll/converters/scss.rb#L74-L80).
>
> ---
> 
> ##### [wjdp](https://github.com/wjdp) commented on 27 Jan 2015
> 
> Thanks! For anyone stumbling on this later:
>
> ```yaml
> sass:
>     load_paths:
>         - _sass
>         - bower_components
> ```
> 
> ---
> 
> * [Accept multiple Sass load paths · Issue #3366 · jekyll/jekyll](https://github.com/jekyll/jekyll/issues/3366)

**\_config.yml** に `sass: load_paths:` で sass の `@import` 先を複数記述できる。
ただし、セーフモードでは動作しない。

github のビルドオプションを見てみると

> #### Configuration settings you cannot change
> 
> GitHub Pages & Jekyll override the following settings in your *\_config.yml* file, which you cannot change:
> 
> ```yaml
> lsi: false
> safe: true
> source: [your repo's top level directory]
> incremental: false
> highlighter: rouge
> gist:
>   noscript: false
> kramdown:
>   math_engine: mathjax
> ```
>
> ---
> * [Configuring Jekyll - User Documentation](https://help.github.com/articles/configuring-jekyll/#configuration-settings-you-cannot-change)

`safe: true` なのでセーフモードでビルドする。この方法も使えない。





## シンボリックリンクが利用できる


> # GitHub Pages symlink test
> 
> Symbolic links work with [GitHub Pages](http://pages.github.com/)!
> 
> ## Test
> 
> 1. Symlink to a file: [this](http://s4y.github.io/gh-pages-symlink-test/link.txt) should show the same content as [this](http://s4y.github.io/gh-pages-symlink-test/target.txt).
> 2. Symlink to a directory: [this](http://s4y.github.io/gh-pages-symlink-test/link/) should show the same content as [this](http://s4y.github.io/gh-pages-symlink-test/target/).
> 
> [Questions?](https://github.com/s4y/gh-pages-symlink-test/issues)
> 
> ---
> 
> * [s4y/gh-pages-symlink-test](https://github.com/s4y/gh-pages-symlink-test)
> * [GitHub Pages symlink test](https://s4y.github.io/gh-pages-symlink-test/)

@s4y が検証しているように、github pagesの要素の中にシンボリックリンクがあった場合も正常に動作するらしい。

submodule を入れるディレクトリを作って、シンボリックリンクを貼る方法が現実的かな。






## シンボリックリンクの動作を検証する

試しに今まで **js** ディレクトリ内に submodule として導入していた MathJax を移動し、シンボリックリンクを貼って動作するか検証する。

まずは submodule を削除する。

> v1.8.3から `git submodule deinit` が追加され、わずらわしかったサブモジュールの削除がほんの少しだけ楽になりました。
> 
> ```sh
> $ git submodule deinit path/to/submodule
> $ git rm path/to/submodule
> $ git config -f .gitmodules --remove-section submodule.path/to/submodule
> ```
> 
> ちなみにv1.8.5からは最後の行もいらなくなるそうです。
> 
> ---
> 
> * [git submoduleを今風な感じで削除する - Qiita](http://qiita.com/u1aryz/items/8d1923da79158439eeaa)

```sh
git submodule deinit js/Mathjax
git rm js/MathJax
git commit
```

submodule をインストールするディレクトリを作り、submoduleとして再びMathjaxをインストール。

```sh
mkdir Submodules
git submodule add https://github.com/mathjax/MathJax Submodules/MathJax
```

MathJaxリポジトリに移動、参照するバージョンを確認する。


> ## tagの一覧を表示する
> 
>     git tag      // アルファベット順に表示される
> 
> ---
> 
> * [【Git】tag関連コマンド - Qiita](http://qiita.com/chihiro/items/cba40015b1aa2c73b78a)

```sh
cd Submodules/Mathjax
git tag
.......
2.6.1
2.6.1-rc.1
2.7.0
2.7.0-beta.0
2.7.1
```

MathJaxのバージョンを **2.7.1** にして保存

> ```sh
> cd submodule_directory
> git checkout v1.0
> cd ..
> git add submodule_directory
> git commit -m "moved submodule to v1.0"
> git push
> ```
> 
> でうまくいった。
> stackoverflow++
> 
> [http://stackoverflow.com/questions/1777854/git-submodules-specify-a-branch-tag](http://stackoverflow.com/questions/1777854/git-submodules-specify-a-branch-tag)
> 
> ---
> 
> * [git submodule でブランチ/タグをチェックアウトする - Qiita](http://qiita.com/kysnm/items/c083674deac0c4a34756)

```sh
git checkout 2.7.1
cd ../../
git add .
```

**js/Mathjax** からシンボリックリンクを作成

```sh
cd ../../js
ln -sf ../Submodules/MathJax MathJax
```

**Submodules** ディレクトリがビルドでコピーされるので除外する。 

**\_config.yml** に `exclude : Submodules` 追加

```yaml
exclude: 
  - Gemfile
  - Gemfile.lock
  - vendor
  - Submodules/
```

これで動くか確認する。

\\[
\begin{eqnarray}
  \left\\{
    \begin{array}{l}
      x + y = 10 \\\\\
      2x + 4y = 32
    \end{array}
  \right.
\end{eqnarray}
\\]

```
** ERROR: directory is already being watched! **

Directory: /path/to/site/Submodules/MathJax

is already being watched through: /path/to/site/Submodules/MathJax

MORE INFO: https://github.com/guard/listen/wiki/Duplicate-directory-errors
```

`jekyll serve`で `listen` がエラーを吐くが、ビルド自体は問題ないみたい。






## Bootstrapを submodule としてインストールする

Bootstrapを **Submodules/Bootstrap** にsubmodule としてインストールする

```sh
git submodule add https://github.com/twbs/bootstrap Submodules/Bootstrap
```

Bootstrapのタグを確認、 Ver.4 の最新版を利用する

```sh
cd Submodules/Bootstrap
git tag
v1.0.0
.......
v4.0.0-alpha
v4.0.0-alpha.2
v4.0.0-alpha.3
v4.0.0-alpha.4
v4.0.0-alpha.5
v4.0.0-alpha.6
```

```sh
git checkout v4.0.0-alpha.6
cd ../../
git add .
```

古いbootstrapを削除する

```sh
rm -r js/bootstrap
rm -r _sass/bootstrap
rm -r font/bootstrap
```

**Submodules/Bootstrap** へシンボリックリンクを行う

> ## Bootstrap source code
> 
> The Bootstrap source code download includes the precompiled CSS, JavaScript, and font assets, 
> along with source Less, JavaScript, and documentation. More specifically, it includes the following and more:
> 
> ```sh
> bootstrap/
> ├── less/
> ├── js/
> ├── fonts/
> ├── dist/
> │   ├── css/
> │   ├── js/
> │   └── fonts/
> └── docs/
>     └── examples/
> ```
> 
> The `less/`, `js/`, and `fonts/` are the source code for our CSS, JS, and icon fonts (respectively). 
> The `dist/` folder includes everything listed in the precompiled download section above. 
> The `docs/` folder includes the source code for our documentation, and `examples/` of Bootstrap usage. 
> Beyond that, any other included file provides support for packages, license information, and development.
> 
> ---
> 
> * [ Getting started · Bootstrap ](https://getbootstrap.com/getting-started/#whats-included-source)

**Bootstrap/dist** の中はコンパイル済みの css/js が入っている。
**Bootstrap/scss** に css になる前の scss ソースコードが入っている。

なので

js/bootstrap
: -> `Submodules/Bootstrap/dist/js`

_sass/bootstrap
: -> `Submodules/Bootstrap/scss`

にそれぞれリンクを貼る

```sh
cd _sass
ln -sf ../Submodules/Bootstrap/scss bootstrap
cd ../js
ln -sf ../submodules/Bootstrap/dist/js bootstrap
```

実際に動作しているか確認する。

