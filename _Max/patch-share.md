---
layout : post
title  : "Max: パッチを共有する"
date   : 2017/03/21
lastchange : 2017-03-24 18:00:24.
tags   :
  - Max
  - share
  - patch
  - compressed
---

## `begin_max5_patcher`から始まる謎の暗号

```
----------begin_max5_patcher----------
229.3ocSPssZCCCC8Y6uBidNazj1tV12wdaLFNohLURrCxNkTJ8ee1xIi8hD
mitcz4gVAs9EL.l2MeZTpGZkRnxDpUrBFsKcC1fzFLhgfsGgpRsHtDE9OXxN
XZwdxYRCbzLYic+f7Vit4Q+bb.ixZpWYklHW+2L1EKxn9vtW2UYpOcTRGZxo
lTz705Pk8DuOgkI.3uRg38AgE1tKcQf91qur+eZgbaRoIy8TqyAYOfcZ5FxA
x6DeH6BIO3pmyvSUBjbE3dAx3MZq+Fgwxo+JldpYtnmkyuAkQ8WP1MShMnyW
9o9W2V3WR.
-----------end_max5_patcher-----------
```

この謎の暗号は何？ というお話。

参考:

* [\---\---\---\-begin\_max5\_patcher\---\---\---\- - monome](http://archive.monome.org/community/discussion/3054/-begin_max5_patcher-/p1.html)
* [Sharing Patches](https://docs.cycling74.com/max7/vignettes/sharing_patches)
* [ブログや電子掲示板にMaxパッチを貼り付ける方法 - 丸井綜研](http://marui.hatenablog.com/entry/20140119/1390107778)

## スケッチをWebで公開する

* [ブログや電子掲示板にMaxパッチを貼り付ける方法 - 丸井綜研](http://marui.hatenablog.com/entry/20140119/1390107778)

に載っているので簡単に。

> ##### [c1t1zen](http://archive.monome.org/community/profile/48/c1t1zen.html)
> 
> ###### [December 2008](http://archive.monome.org/community/discussion/3054/-begin_max5_patcher-/p1.html)
> 
> how do I open the text files that start with
> 
> ```
> ----------begin_max5_patcher----------
> ```
> 
> I need like a dummy proof step by step explanation. 
> I've read a couple forums that include these but 
> haven't found a good run through or even what they are named to help me search better.
> 
> ---
> 
> ##### [soundcystsoundcyst](http://archive.monome.org/community/profile/7/soundcyst.html)
> 
> ###### [December 2008](http://archive.monome.org/community/discussion/comment/34008.html#Comment_34008)
> 
> this is one of the biggest pains about max 5, particularly the lack of documentation regarding this feature.
> 
> anyways, copy the text (ctrl c, apple c, whatever) 
> starting at the beginning of the `begin_max5_patcher` line,
> ending at the end of the `end_max5_patcher` line
> 
> then open up max 5, and click on the file menu, and select New From Clipboard.
> 
> ---
>
> * [\---\---\---\-begin\_max5\_patcher\---\---\---\- - monome](http://archive.monome.org/community/discussion/3054/-begin_max5_patcher-/p1.html)

> > ## Copying a patcher into an e-mail message in compressed format
> >
> > Max offers a compressed text format for copying patcher data to the clipboard. 
> > This may be useful when posting excerpts of a patcher on a public forum or mailing list.
> > The text is "unreadable" but will be a small fraction the size of the usual text format.
> > 
> > * Select the part of the patch you wish to copy
> > * Choose Copy Compressed from the Edit menu to copy the selected objects to the clipboard
> > * Switch to your e-mail client. Paste the contents of the clipboard into a message
>
> ---
> 
> * [Sharing Patches](https://docs.cycling74.com/max7/vignettes/sharing_patches)

{% capture url %}{{ site.github.url }}{% link _Max/images/patch-share/01_copy_compressed.png %}{% endcapture %}
{% assign caption = '

共有したい部分を選択し、`Edit -> Copy Compressed`でMaxパッチの選択した場所が

```
----------begin_max5_patcher----------
1126.3oc6b90bhBCD.+Y7SACOaug.nB2a2miN2vDfnl6fDFHzZuN869ER.DP
Qs+w6ZG2Wv5t4O6tIY4GqZedlgUDeGozx76l2aZX77LCCknZAFMu2vJCuKNE
WpZlEi7HO5WVy0pDjcBk3bym3UEg3Gwk7LRXNVDussQrpLdkHkHTi.pQppIT
1lvBRrPaAtAdeydt4R65qHW+5Wbb9ls4Oa5idXDOkSzcvJBy1X0otT7TpRiU
6TSSTuUZw2gPsByKHkDl.KnbVuY2IXQ8D5qlV6lKciszInri5CjhlvUS7xvZ
MMk7.onTN98ZsgENOumXidcoNH+KtZfVMuSDkoE41Ipf7.ss+NcRwExHoP5H
UEZ2em+Rq8CCOgTvpnJSQK7kYslzd2egiqJdGrRsF3oh.d98hAxsFaR4w+ln
Bp1sB44DFk0OnNPcBYMtJUDtlyDkz+nLPjbQ8X5WiiIS1YFNS6c+nfhSa8Oq
MEzDNq1HFDqqE2Nc2ahTKs0W26LpVvv4Goyx8Kx3xDJKkNYUYDtndoHRugqc
svRv4oCU00uTxZQi5bJiMJJJ34SqrftY6I5aDWpL6TisRSYXESqMTdbVDVhe
XXzVfSSaNeOb32gYzLrfHn5k.G6NkDFV5naKiK3ooC7WslGNhlD4t3XxizDw
V0D0eyfr4z71MQVcqxIzMjRwPYB7lxgRNHAfTTUTyozPAIKOU5ECavfLf8OR
1OS3.4ixHFUIi9rtyaSkvapjdH0gA4dSUtOmtqcaTOehuI78QI.8FYh8Rm0J
+kYyZ+i4uwng1RmNZXeQQC4Fr5Wb6t95bUmoc0dJh4YYxTVGNPrDxtO53hxD
d2aRrcNQX4iXSBZxHm86MxsOyaJkM0QNkuWq+3gzRIjQbq60ro1bXTPlBQPY
c2G59tcDiZ2VZRBgM1yRnk0Ysz4Ounk+WqEitPK16CxhM+4r92x2pNqeRn9N
bgXgnfJSfoWN5yv7pxDKwBhvoM4Y6tO8oRKuO28r81q5pNF+IAEEsx4pvhZe
FVTjFXwYABfQAXT.FEfQAXT.FEfQAXT.F81CFcwRMwfav0.FM3LrntdJHLjW
.vhBrn.KJvhBrn.KJvh94lE8eMY27ubzy.K5apvnN57PK8tFrn9mgE8NukZV
LOfEEXQAVTfEEXQAVTfEEpKJTWza15h5hVcMXQWctuunqTSqqO7YzCrn.KJv
hBrn.KJvhB0EEpK5s6WXTGmqRcQWdNVTeWntn.KJvhBrn.KJvhBrnPcQg5hd
y+iWxEcMXQWb1e6RA5rfPcQAVTfEEXQAVTfEEXQg5hB0E8FttnWkOhdOK.pD
fJAnR.pDfJAnxuXPkPwMgha9ECnT4zC2jn4KGGpanLOHD62OzMQ3c0f1bPn8
vv5AztWp0D7oxZp+mB0YMmkuFyokvtHQSA+Ov97eO1G5cYeKt.yajObUWNQW
f8r3+W35hruf+ea27tjkSzab4TmHazS7UaIidRuQOk2gOg2zOc23mrSNyuL6
ufJUd1.
-----------end_max5_patcher-----------
```

このようにコピーされる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

## 公開されているスケッチを開く

> > ## Opening a patcher from an e-mail message
> > 
> > Max can create objects from text on the clipboard in the old Max 4 text format, standard Max format, or the compressed format.
> > 
> > * Because there may be a lot of text involved in the patcher data,
> >   the easiest technique for selecting all of the data is select a line at the beginning of the text,
> >   then scroll to the end of the message and Shift-click to select the end of the text.
> > * Once all of the text is selected, choose Copy from the Edit menu in your e-mail client. 
> >   If you are manually selecting all the data,
> >   be sure to include the `begin_max5_patcher` and `end_max5_patcher` lines in their entirety.
> > * Switch to Max.
> > * If you want to create a new patcher window with the patcher data you've received,
> >   choose New From Clipboard from the File menu. 
> >   A new patcher window will appear with the objects from the e-mail message.
> > * If you want to paste the objects in the e-mail message
> >   into an existing unlocked patcher, choose Paste from the Edit menu.
> 
> ---
> 
> * [Sharing Patches](https://docs.cycling74.com/max7/vignettes/sharing_patches)

{% capture url %}{{ site.github.url }}{% link _Max/images/patch-share/02_new_from_clipboard.png %}{% endcapture %}
{% assign caption = '

貼り付けられている暗号を

    ----------begin_max5_patcher----------

から

    -----------end_max5_patcher-----------

までコピーしたら、Max側で`File -> New From Clipboard`
または`Command + Option + N`を押すと共有されているパッチを開くことができる。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

