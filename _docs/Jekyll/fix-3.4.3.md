---
layout : post
title  : github-pages jekyll 3.4.3不具合の対処法
date   : 2017/06/01
lastchange : 2017-06-01 20:40:01.
tags   :
  - jekyll
  - github
  - pages
  - 3.4.3
  - bug
---

## ローカルデバッグ時警告が出てリンクが切れる

Github-pagesを更新したら

```
GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
```

という警告が出てリンク切れをおこしたので対処法を調べた。

>  [asnowfix](https://github.com/asnowfix) commented 16 days ago
> 
> Fixed my issue (not saying it is yours) by adding...
> 
>     github: [metadata]
> 
> ...to `_config.yml`
> 
> ---
> 
> * ["No GitHub API authentication" error · Issue #399 · github/pages-gem](https://github.com/github/pages-gem/issues/399)

**_config.yml** に上記のものを記述すると修正されるらしいので変更する。

```yml
# ---- fixed github metadata authentication bug ----
github : [metadata]
```

ローカルのリンクは直ったが、今度はgithub-page上でリンク切れが。

```yml
# ---- fixed github metadata authentication bug ----
# github : [metadata]
```

適宜コメント有無を使い分けるしかないか・・・。
