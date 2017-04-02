---
layout : post
title  : "Max: サンプリングレートで動作を分ける"
date   : 2017/04/02
lastchange : 2017-04-02 18:47:02.
tags   :
  - Max
  - Max for Live
  - Sampling Rate
  - HPF
  - High Pass

---

## \\( 44.1 \mathrm{Hz} \\) で \\(30000 \mathrm{Hz} \\) のHigh Pass Filter(HPF)かけたらDSPがフリーズした。

{% capture url %}{{ site.github.url }}{% link _Max/images/separate-samplingrate/01_Oops.png %}{% endcapture %}
{% assign caption = '音が一切出なくなった。' | markdownify %}
{% include thumbnail.html url=url caption=caption %}

ので固定値じゃなくて、サンプリングレートで分けて高周波の閾値を設定してあげねばならない。
おそらくナイキスト周波数、\\(44.1 \mathrm{kHz} \\) の場合\\(22050 \mathrm{Hz} \\)以上のフィルターをかけるとフリーズするのでそれ以下に抑える。


## サンプリングレートを取得して場合分けする。

`dspstate~`を利用する。

> ## Description
> 
> dspstate~ can be used for calculations 
> that require the sampling rate of current DSP processing block size.
> You can also use the leftmost outlet to trigger some event when the audio is turned on or off. 
> 
> ---
>
> ## Output
> 
> ### float
> 
> Out second outlet: Sampling rate of the connected signal or the global sampling rate.
> 
> ---
> 
> * [Max 7 - dspstate~ Reference](https://docs.cycling74.com/max7/maxobject/dspstate~)

第二アウトレットからサンプリングレートがでてくるらしい。

{% capture url %}{{ site.github.url }}{% link _Max/images/separate-samplingrate/02_patch.png %}{% endcapture %}
{% assign caption = '
    ----------begin_max5_patcher----------
    447.3ocuU00aCBBE8Y8WAgmsMheUcIa+QVVVvJqiFKZ.ryllte6iOzt1k1na
    yVdPy8buv8btvE165.yqZIBH3Avy.Gm8tNNFHMfSmsCbCtcYIVXBCxHeTkuF
    5YcIIsRCLGLa1rx7dbgbWIw3nGg0ropQVRjlkA0gVikKemxV8JmrTZoQV.Zt
    uGHHIP+KNzXnf.uzMG6xH2USrS.BO5hVXxohfyPYmjYJqOw9ZrCtt5Od+OIK
    AA9pw7IPyHe+XsNCyrhNaXQ+VYEVdQkGdQgilTgGMQBOKLYdnZrHZhDezsV7
    BRI.MVkGLRkmXNxmtXdhZDmLj7ywrUPuqbvO9hUffoqB7zifnzaw9evBcUHI
    d38eJ6x69o2ZsWHpERrj74X0dzvWzkE+ajsW+oeui1WqZfRGrYvLMXIk8yGA
    L7UiedMRT0vW1SmtJN3aFWPDRJCKoUrShI9rXdmVTPLt86mEUfyKIEW+94wx
    l3Qvln6JaPCvlv6FazOHNXwAkd+3S5HJO+U5XOWiqq2R3htkzvDUW95Jt1bg
    mwjxrlgFSNYKsOdyk2PLW02JUMsMbaedah8MF3lpBBm0P65oTY9f6WvoEpQ4
    -----------end_max5_patcher-----------

サンプル

`live.thisdevice`からの *bang* を `dspstate~`に入れ、
サンプリングレートを第二アウトレットから取得。\\( 48 \mathrm{kHz}\\)未満の場合、
高周波の上限を\\(20 \mathrm{kHz}\\)に抑える。それ以外は\\(24 \mathrm{kHz}\\)。
\\(44.1 \mathrm{kHz}\\)以下のことは考えてない。

' | markdownify %}
{% include thumbnail.html url=url caption=caption %}


