---
layout : post
title  : Github Pagesにbootstrap sassをインストールする
date : 2017/01/20
lastchange : 2017-06-15 22:39:38.
tags   :
  - jekyll
  - ruby
  - rbenv
  - macOS
  - github
  - pages
---

## Github Pagesを簡単にレスポンシブにしたい

ので、[Bootstrap](http://getbootstrap.com/)を利用したい。

レスポンシブの意味合いが変化しているように感じる。

2000年初め
  : ウィンドウサイズを変更したときにサイズに応じて各要素が変化する。cssで幅、高さを`%`指定などで実装

最近
  : 魑魅魍魎の画面サイズに対応したレイアウトに変化する。

ということで、スマートフォンでも問題ないレイアウトでページが組みたい。

## Bootstrapのインストール

参考

* jekyllへのbootstrapのインストール概略
  * [My Jekyll Blog Setup with Bootstrap, SASS and Pygments](https://kvurd.com/blog/my-jekyll-blog-setup-bootstrap-sass-pygments/)
  * [Jekyll with Twitter Bootstrap Sass](http://jekyll.pygmeeweb.com/2014/08/02/jekyll-with-twitter-bootstrap-sass/)
* SASSについて、jekyllでのSASSコンパイルについて
  * [CSSのメタ言語Sass(SCSS)、LESSの完全入門](http://qiita.com/ritukiii/items/67b3c50002b48c6186d6)
  * [Jekyll - Assets](http://jekyllrb-ja.github.io/docs/assets/)
    * [Jekyll - Assets](https://jekyllrb.com/docs/assets/)
* Bootstrap sassについて
  * [Bootstrap 4を使うならSassを使って3倍幸せになろう](http://qiita.com/tonkotsuboy_com/items/1855734522bfe7ef7dad)
  * [全部はいらないよね？bootstrap-sassをカスタマイズして使う方法](http://qiita.com/masamitsu-konya/items/e3630046774ac1fbd346)
* BootstrapとjQueryとか
  * [Bootstrapの使い方（１）〜準備](http://itstudio.co/2015/04/06/3816/)
  * [Bootstrap 3.3.7 released](http://blog.getbootstrap.com/2016/07/25/bootstrap-3-3-7-released/)
  * [Bootstrapをはじめる。対応しているjQueryのバージョンなど。](http://code-examples-ja.hateblo.jp/entry/2014/07/28/Bootstrap%E3%82%92%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%80%82%E5%AF%BE%E5%BF%9C%E3%81%97%E3%81%A6%E3%81%84%E3%82%8BjQuery%E3%81%AE%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%81%AA%E3%81%A9)
* Bootstrapインストール確認、インストール後Tips
  * [GitHub PagesでJekyllを使う場合、cssファイルなどへのパスには`{{site.github.url}}`を使うと良い](http://blog.scheakur.com/post/90240750827/github)
  * [Components](http://getbootstrap.com/components/)

### Bootstrapのダウンロード

{% assign text='
## Get Twitter Bottstrap Sass files

* Download zip archive from [release page](https://github.com/twbs/bootstrap-sass/releases).
' | markdownify %}
{% assign source='
[Jekyll with Twitter Bootstrap Sass](http://jekyll.pygmeeweb.com/2014/08/02/jekyll-with-twitter-bootstrap-sass/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



{% assign text='
Download the latest version of [bootstrap-sass](https://github.com/twbs/bootstrap-sass). Extract it to a temporary folder. 
' | markdownify %}
{% assign source='
[My Jekyll Blog Setup with Bootstrap, SASS and Pygments](https://kvurd.com/blog/my-jekyll-blog-setup-bootstrap-sass-pygments/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





ということで、bootstrap-sassをダウンロードしてくる。
bootstrap-sassのダウンロード先だが、本家WebサイトのDownloadにした。

Getting startedの

{% assign text='
### Sass

[Bootstrap ported from Less to Sass](https://github.com/twbs/bootstrap-sass) for easy inclusion in Rails, Compass, or Sass-only projects.

[Download Sass](https://github.com/twbs/bootstrap-sass/archive/v3.3.7.tar.gz)
' | markdownify %}
{% assign source='
[Bootstrap - Getting started](http://getbootstrap.com/getting-started/#download)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




からダウンロードする。

ダウンロードしたらテキトーなディレクトリに展開する。

### jQueryのダウンロード

jQueryのサイトから圧縮版をダウンロードする。

{% assign text='
[Download the compressed, production jQuery 3.1.1](https://code.jquery.com/jquery-3.1.1.min.js)
' | markdownify %}
{% assign source='
[Download jQuery \| jQuery](https://jquery.com/download/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





### Bootstrapのインストール

展開したファイルを適切なディレクトリに移動させて、jekyllの設定を変更する。

行数が多いのでセクション毎にわけている。

#### __\_config.yml__ を変更する

まずはjekyllの設定を行う。

{% assign text='
Add the following line to your \_config.yml file:

```
sass:
    sass_dir: _sass
```
' | markdownify %}
{% assign source='
[My Jekyll Blog Setup with Bootstrap, SASS and Pygments](https://kvurd.com/blog/my-jekyll-blog-setup-bootstrap-sass-pygments/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


__\_config.yml__ にsassが配置されるディレクトリを指定する。
引用の通り、 __\_sass__ ディレクトリに配置することにする。


#### __\_sass__ ディレクトリの作成

{% assign text='
Create a SASS import directory. This is the folder that jekyll will tell SASS to use when you @import things.

```sh
$ cd my_jekyll_blog
$ $ mkdir _sass
```
' | markdownify %}
{% assign source='
[My Jekyll Blog Setup with Bootstrap, SASS and Pygments](https://kvurd.com/blog/my-jekyll-blog-setup-bootstrap-sass-pygments/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




__\_config.yml__ に設定した通り、__\_sass__ ディレクトリをjekyllプロジェクトページに作成する。

```sh
mkdir _sass
```

#### Bootstrapのsassファイルインストール


{% assign text='
Download the latest version of [bootstrap-sass](https://github.com/twbs/bootstrap-sass).
Extract it to a temporary folder.
Then copy the `assets/stylesheets/bootstrap.scss` file and 
`assets/stylesheets/bootstrap/`
folder to your blog SASS import directory. (`\_sass`).
' | markdownify %}
{% assign source='
[My Jekyll Blog Setup with Bootstrap, SASS and Pygments](https://kvurd.com/blog/my-jekyll-blog-setup-bootstrap-sass-pygments/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


{% assign text='
### Get Twitter Bottstrap Sass files

* Download zip archive from [release page](https://github.com/twbs/bootstrap-sass/releases).
* Unzip the `assets` folder at the root of your site.

' | markdownify %}
{% assign source='
[Jekyll with Twitter Bootstrap Sass](http://jekyll.pygmeeweb.com/2014/08/02/jekyll-with-twitter-bootstrap-sass/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




片方は __\_sass__ 直下、片方は __assets__ を丸ごとコピーしており、それぞれ __\_config.yml__ の記述方法も違う。

__\_sass__ 直下も __assets/stylesheets__ をsass用ディレクトリにするのも違う気がするので、
Bootstrapを展開したディレクトリの __assets/stylesheets__ ディレクトリを __\_sass/bootstrap__ としてコピーする
という方法にした。後から __javascripts__ 、 __fonts__ ディレクトリも別の場所に配置する。

```sh
cp -r ../bootstrap-sass/assets/stylesheets _sass/bootstrap
```

#### Bootstrapのjavascriptファイルインストール

次はjavascriptsを移動させる。これらは __js/bootstrap__ ディレクトリに配置する。
ビルドされる必要はないが、出力されるWebサイトデータにコピーされていなければならないので、
__\_sass__ 等に配置はできない。

後ほど __js__ ディレクトリにはjQueryもインストールする。

```sh
mkdir js
cp ../bootstrap-sass/assets/javascripts js/bootstrap
```


#### Bootstrapのfontファイルインストール

フォントは __fonts__ に移動させる。bootstrapのフォント以外を利用する予定がないため。

```sh
mkdir fonts
cp ../bootstrap-sass/assets/fonts .
```

#### 自前のsassファイル作成

ここまできたらbootstrap sassを自作のsassファイルで `@import` する。
ひとまず、自前の大域的に利用するスタイルシートで利用するので、
__css/style.sass__ ファイルを作成して利用する。

```sh
mkdir css && cd css
touch style.sass
```



{% assign text='
Jekyll は Sass と CoffeeScript の組み込みをサポートしています。
それらを使うためには、適切な拡張子名のファイルを作成( `.sass`, `.scss` や `.coffee` の一つ) し、
ファイルを2行の3ダッシュで開始します、このように:

```css
---
---

// コンテンツのはじまり
.my-definition
  font-size: 1.2em
```

Jekyll はこれらのファイルを通常ページと同一に扱われ、 その中で出力ファイルはもとファイルと同一ディレクトリに配置されます。 
例えば、あなたが `/css/styles.scss` という名前のファイルを
サイトのソースフォルダに持っていた場合、 Jekyll はそれを処理し、そしてサイトの指定フォルダの下に `/css/styles.css` を配置します。
' | markdownify %}
{% assign source='
* [Jekyll - Assets](http://jekyllrb-ja.github.io/docs/assets/)
    * [Jekyll - Assets](https://jekyllrb.com/docs/assets/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



__style.sass__ ファイルの中身は以下

```css
---
---

@import "bootstrap/_bootstrap"
```

#### jQueryのインストール

jQueryを __js__ ディレクトリにインストールする。

```sh
cp ../jquery-3.1.1.min.js js/.
```

#### ディレクトリ構成まとめ

参考

* [tree コマンドが無い環境で tree コマンドを実現](http://qiita.com/yone098@github/items/bba8a42de6b06e40983b)

ここまでのディレクトリ構成を図解

```sh

github-pages/
|--.DS_Store
|--.bundle
|--.gitignore
|--.ruby-version
|--Gemfile
|--Gemfile.lock
|--_config.yml
|--_layouts
|  |--default.html
|--_posts
|--_sass
|  |--bootstrap
|  |  |--.DS_Store
|  |  |--_bootstrap-compass.scss
|  |  |--_bootstrap-mincer.scss
|  |  |--_bootstrap-sprockets.scss
|  |  |--_bootstrap.scss
|  |  |--bootstrap
|  |  |  |--.DS_Store
|  |  |  |--_alerts.scss
|  |  |  |--_badges.scss
|  |  |  |--_breadcrumbs.scss
|  |  |  |--_button-groups.scss
|  |  |  |--_buttons.scss
|  |  |  |--_carousel.scss
|  |  |  |--_close.scss
|  |  |  |--_code.scss
|  |  |  |--_component-animations.scss
|  |  |  |--_dropdowns.scss
|  |  |  |--_forms.scss
|  |  |  |--_glyphicons.scss
|  |  |  |--_grid.scss
|  |  |  |--_input-groups.scss
|  |  |  |--_jumbotron.scss
|  |  |  |--_labels.scss
|  |  |  |--_list-group.scss
|  |  |  |--_media.scss
|  |  |  |--_mixins.scss
|  |  |  |--_modals.scss
|  |  |  |--_navbar.scss
|  |  |  |--_navs.scss
|  |  |  |--_normalize.scss
|  |  |  |--_pager.scss
|  |  |  |--_pagination.scss
|  |  |  |--_panels.scss
|  |  |  |--_popovers.scss
|  |  |  |--_print.scss
|  |  |  |--_progress-bars.scss
|  |  |  |--_responsive-embed.scss
|  |  |  |--_responsive-utilities.scss
|  |  |  |--_scaffolding.scss
|  |  |  |--_tables.scss
|  |  |  |--_theme.scss
|  |  |  |--_thumbnails.scss
|  |  |  |--_tooltip.scss
|  |  |  |--_type.scss
|  |  |  |--_utilities.scss
|  |  |  |--_variables.scss
|  |  |  |--_wells.scss
|  |  |  |--mixins
|  |  |  |  |--_alerts.scss
|  |  |  |  |--_background-variant.scss
|  |  |  |  |--_border-radius.scss
|  |  |  |  |--_buttons.scss
|  |  |  |  |--_center-block.scss
|  |  |  |  |--_clearfix.scss
|  |  |  |  |--_forms.scss
|  |  |  |  |--_gradients.scss
|  |  |  |  |--_grid-framework.scss
|  |  |  |  |--_grid.scss
|  |  |  |  |--_hide-text.scss
|  |  |  |  |--_image.scss
|  |  |  |  |--_labels.scss
|  |  |  |  |--_list-group.scss
|  |  |  |  |--_nav-divider.scss
|  |  |  |  |--_nav-vertical-align.scss
|  |  |  |  |--_opacity.scss
|  |  |  |  |--_pagination.scss
|  |  |  |  |--_panels.scss
|  |  |  |  |--_progress-bar.scss
|  |  |  |  |--_reset-filter.scss
|  |  |  |  |--_reset-text.scss
|  |  |  |  |--_resize.scss
|  |  |  |  |--_responsive-visibility.scss
|  |  |  |  |--_size.scss
|  |  |  |  |--_tab-focus.scss
|  |  |  |  |--_table-row.scss
|  |  |  |  |--_text-emphasis.scss
|  |  |  |  |--_text-overflow.scss
|  |  |  |  |--_vendor-prefixes.scss
|--_site
|--css
|  |--style.sass
|--fonts
|  |--bootstrap
|  |  |--glyphicons-halflings-regular.eot
|  |  |--glyphicons-halflings-regular.svg
|  |  |--glyphicons-halflings-regular.ttf
|  |  |--glyphicons-halflings-regular.woff
|  |  |--glyphicons-halflings-regular.woff2
|--index.md
|--js
|  |--bootstrap
|  |  |--.DS_Store
|  |  |--bootstrap
|  |  |--bootstrap-sprockets.js
|  |  |--bootstrap.js
|  |  |--bootstrap.min.js
|  |  |  |--affix.js
|  |  |  |--alert.js
|  |  |  |--button.js
|  |  |  |--carousel.js
|  |  |  |--collapse.js
|  |  |  |--dropdown.js
|  |  |  |--modal.js
|  |  |  |--popover.js
|  |  |  |--scrollspy.js
|  |  |  |--tab.js
|  |  |  |--tooltip.js
|  |  |  |--transition.js
|  |--jquery-3.1.1.min.js
|--vendor
```

#### レイアウトデータでの読み込み

{% assign text='
### テンプレート

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge" charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link href="css/bootstrap.min.css" rel="stylesheet">
  <!--[if lt IE 9]>
    <script src="js/html5shiv.js"></script>
    <script src="js/respond.min.js"></script>
  <![endif]-->
  <script src="js/jquery.min.js"></script>
  <script src="js/bootstrap.min.js"></script>
</head>
<body>


</body>
</html>
```
' | markdownify %}
{% assign source='
[Bootstrapをはじめる。対応しているjQueryのバージョンなど。](http://code-examples-ja.hateblo.jp/entry/2014/07/28/Bootstrap%E3%82%92%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%80%82%E5%AF%BE%E5%BF%9C%E3%81%97%E3%81%A6%E3%81%84%E3%82%8BjQuery%E3%81%AE%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%81%AA%E3%81%A9)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




__\_layouts/default.html__ でjQueryとbootstrapを呼び出す。
引用元と少し変える部分は、bootstrapのcssを直接呼び出すのではなく、 __css/style.css__ を呼び出すのと、
各々配置されているディレクトリ名が変わっていること。 __css/style.css__ は __css/style.sass__ がビルドされて出力されたもの。




{% capture text %}

{% raw %}
では、どうすればいいのでしょうか。 正解は GitHub Pages だけで有効になるsite変数を使うことです。
以下のページに記載されていますが、GitHub Pages上ではさまざまなメタデータをsite変数として利用可能です。

```sh
https://help.github.com/articles/repository-metadata-on-github-pages
```

cssファイルなどへのパスに利用したい場合は site.github.url を使うと良いでしょう。つまり、以下のように記述するわけです。

```
<link rel="stylesheet" href="{{site.github.url}}/css/style.css" charset="utf-8">
```

このように記述しておくと、GitHub Pages上では以下のように展開され、正しくファイルを指定できます。
{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
{% raw %}
[GitHub PagesでJekyllを使う場合、cssファイルなどへのパスには`{{site.github.url}}`を使うと良い](http://blog.scheakur.com/post/90240750827/github)
{% endraw %}
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




ということなので、パスの指定に`{{site.gitub.url}}`を利用する。

{% raw %}
```html

<!DOCTYPE html>
<html lang="ja">

  <head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ page.title }}</title>

    <!-- bootstrap and jQuery include -->
    <link rel="stylesheet" href="{{site.github.url}}/css/style.css">
    <script type="text/javascript" src="{{site.github.url}}/js/jquery-3.1.1.min.js"></script>
    <script type="text/javascript" src="{{site.github.url}}/js/bootstrap/bootstrap.min.js"></script>
    <!-- bootstrap and jQuery include -->

  </head>
  <body>
    {{ content }}
    <p> - rendered with layout template - </p>
    <button type="button" class="btn btn-default" aria-label="Left Align">
      <span class="glyphicon glyphicon-align-left" aria-hidden="true"></span>
    </button>

    <button type="button" class="btn btn-default btn-lg">
      <span class="glyphicon glyphicon-star" aria-hidden="true"></span> Star
    </button>
  </body>
</html>
```

{% endraw %}

#### ヘッダ部分のモジュール化
なんとなく`<head></head>`部分を他でも流用しそうな気がしたのでモジュール化したいと思った。

{% capture text %}
{% raw %}
| `_includes` | これらは部分的な再利用を容易にするために layouts ファイルと posts ファイルによって、<br>ミックス、マッチすることができます。 liquid のタグ `{% include file.ext %}` は、<br>ファイルの `_includes/file.ext` を含むために使用されます。 |
{% endraw %}
{% endcapture %}
{% assign text=text | markdownify %}
{% assign source='
* [Jekyll - ディレクトリ構成](http://jekyllrb-ja.github.io/docs/structure/)
    * [Jekyll - Directory structure](https://jekyllrb.com/docs/structure/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



__\_includes__ ディレクトリを作成して、中に __defaulthtmlheader.html__ というファイルを作成する。

内容は上記の`<head></head>`と同じ。

{% raw %}
```html
  <head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ page.title }}</title>

    <!-- bootstrap and jQuery include -->
    <link rel="stylesheet" href="{{site.github.url}}/css/style.css">
    <script type="text/javascript" src="{{site.github.url}}/js/jquery-3.1.1.min.js"></script>
    <script type="text/javascript" src="{{site.github.url}}/js/bootstrap/bootstrap.min.js"></script>
    <!-- bootstrap and jQuery include -->

  </head>

```

伴って、 __\_layouts/default.html__ も変更する。

```html
<!DOCTYPE html>
<html lang="ja">
  {% include defaulthtmlheader.html %}
  <body>
    {{ content }}
    <p> - rendered with layout template - </p>
    <button type="button" class="btn btn-default" aria-label="Left Align">
      <span class="glyphicon glyphicon-align-left" aria-hidden="true"></span>
    </button>

    <button type="button" class="btn btn-default btn-lg">
      <span class="glyphicon glyphicon-star" aria-hidden="true"></span> Star
    </button>
  </body>
</html>
```
{% endraw %}

#### テスト

{% assign text='
### Examples

Use them in buttons, button groups for a toolbar, navigation, or prepended form inputs.
Copy

```html
<button type="button" class="btn btn-default" aria-label="Left Align">
  <span class="glyphicon glyphicon-align-left" aria-hidden="true"></span>
</button>

<button type="button" class="btn btn-default btn-lg">
  <span class="glyphicon glyphicon-star" aria-hidden="true"></span> Star
</button>
```
' | markdownify %}
{% assign source='
[Components](http://getbootstrap.com/components/)
' | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



このソースコードを入れてボタンとアイコンがちゃんと設定されるか確認する。

{% capture url %}{{ site.github.url }}{% link _docs/Jekyll/images/jekyll-bootstrap-install/ss.png %}{% endcapture %}
{% assign caption = '' | markdownify %}
{% include thumbnail.html url=url caption=caption %}
