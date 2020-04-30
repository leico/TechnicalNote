---
layout : post
title  : hls 配信で生成され続ける動画ファイルを定期的に削除する
date : 2020/04/30
lastchange : 2020-04-30 17:28:26.
tags   :
  - avconv
  - alsa
  - Webcam
  - video4linux2
  - hls
  - http live streaming
  - cron
  - shell script
---

## 短尺の動画データが大量に貯まっていく

HLS 配信を自前サーバでやっていると、過去の動画が延々と貯まっていく。

```sh
$ ls /var/www/html/*.ts
...
/var/www/html/out52300.ts  /var/www/html/out52323.ts  /var/www/html/out52346.ts
/var/www/html/out52301.ts  /var/www/html/out52324.ts  /var/www/html/out52347.ts
/var/www/html/out52302.ts  /var/www/html/out52325.ts  /var/www/html/out52348.ts
/var/www/html/out52303.ts  /var/www/html/out52326.ts  /var/www/html/out52349.ts
/var/www/html/out52304.ts  /var/www/html/out52327.ts  /var/www/html/out52350.ts
/var/www/html/out52305.ts  /var/www/html/out52328.ts  /var/www/html/out52351.ts
/var/www/html/out52306.ts  /var/www/html/out52329.ts  /var/www/html/out52352.ts
/var/www/html/out52307.ts  /var/www/html/out52330.ts  /var/www/html/out52353.ts
/var/www/html/out52308.ts  /var/www/html/out52331.ts  /var/www/html/out52354.ts
/var/www/html/out52309.ts  /var/www/html/out52332.ts  /var/www/html/out52355.ts
/var/www/html/out52310.ts  /var/www/html/out52333.ts  /var/www/html/out52356.ts
/var/www/html/out52311.ts  /var/www/html/out52334.ts  /var/www/html/out52357.ts
/var/www/html/out52312.ts  /var/www/html/out52335.ts  /var/www/html/out52358.ts
/var/www/html/out52313.ts  /var/www/html/out52336.ts  /var/www/html/out52359.ts
/var/www/html/out52314.ts  /var/www/html/out52337.ts  /var/www/html/out52360.ts
/var/www/html/out52315.ts  /var/www/html/out52338.ts  /var/www/html/out52361.ts
/var/www/html/out52316.ts  /var/www/html/out52339.ts  /var/www/html/out52362.ts
/var/www/html/out52317.ts  /var/www/html/out52340.ts  /var/www/html/out52363.ts
/var/www/html/out52318.ts  /var/www/html/out52341.ts  /var/www/html/out52364.ts
/var/www/html/out52319.ts  /var/www/html/out52342.ts  /var/www/html/out52365.ts
/var/www/html/out52320.ts  /var/www/html/out52343.ts  /var/www/html/out52366.ts
/var/www/html/out52321.ts  /var/www/html/out52344.ts  /var/www/html/out52367.ts
/var/www/html/out52322.ts  /var/www/html/out52345.ts
```

このままでは HDD が溢れてしまうので、定期的にデータを古い順に削除する方法を探った。

参考:

* [cronの設定方法 - Qiita](https://qiita.com/hikouki/items/e744b3a4d356d2af12cf)

## ジョブを定期的に実行する

以下の参考ページが役に立った。


{% capture text %}

## cron設定手順

### 1. cronを設定するためのエディタを起動

`-uオプション`で指定したユーザーで
cronを設定したコマンドが実行されます。

```sh
crontab -u root -e
```

### 2. cronの設定を記述

以下の書式に従って記述します。

```sh
分 時 日 月 曜日 <実行コマンド>
```

#### 固定指定

以下のように設定すると、15時になる度に実行されます。

```sh
0 15 * * * echo "hello."
```

#### 間隔指定

間隔を指定したい場合は、以下のようにします。
例では、5分毎に実行されます。

```sh
*/5 * * * * echo "hello."
```

#### 複数指定

複数時間を設定したい場合はカンマ(,)で区切ります。
例では、午前2時5分と午前2時10分に実行されます。

```sh
5,10 2 * * * echo "hello."
```

#### 範囲指定

複数指定よりも範囲で指定した方がいい場合があります。
以下のようにすると、指定範囲で実行できます。
例では、午前2時5分、午前3時5分、午前4時5分、午前5時5分に実行されます。

```sh
5 2-5 * * * echo "hello."
```

上記の書式で記述後、保存してエディタを閉じると設定完了です。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[cronの設定方法 - Qiita](https://qiita.com/hikouki/items/e744b3a4d356d2af12cf)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


## 実行するジョブを作成する

以下の要件のジョブを作成した。

1. _\*.ts_ ファイルの個数を確認する
2. ファイル数が100個以上存在した場合、数字が若い方から50個を削除する

### ファイルの個数を確認する

以下のシェルスクリプトで個数が導出できることを実験して確かめた。

```sh
$ ls /var/www/html/ | grep -E "*\.ts" | wc -l
```

### 数字が若い方から50個を削除する

今回の場合、ファイル名は `out*.ts` 、 `*` に数字が入ることがわかっている。
この数字が小さい順にソートして、先頭50個を `rm` に渡すことができたら問題なさそうだ。

ということで、上記のシェルスクリプトを少し改良し、

```sh
$ ls /var/www/html/ | grep -E "*\.ts" | sort -t 't' -k 2n,2 | head -50 
```

`out*.ts` なので `t` を区切り文字にして2列めをソートする設定で実現できた。

```sh
$ ls /var/www/html/ | grep -E "*\.ts" | sort -t 't' -k 2n,2 | head -50
out52450.ts
out52451.ts
out52452.ts
out52453.ts
out52454.ts
out52455.ts
out52456.ts
out52457.ts
out52458.ts
out52459.ts
out52460.ts
...
out52499.ts
```

だが、出力結果にファイルパスが足りないので追加する。

```sh
$ ls /var/www/html/ | grep -E "*\.ts" | sort -t 't' -k 2n,2 | head -50 | sed -e 's:^:/var/www/html/:g'
/var/www/html/out52450.ts
/var/www/html/out52451.ts
/var/www/html/out52452.ts
/var/www/html/out52453.ts
/var/www/html/out52454.ts
/var/www/html/out52455.ts
/var/www/html/out52456.ts
/var/www/html/out52457.ts
/var/www/html/out52458.ts
/var/www/html/out52459.ts
/var/www/html/out52460.ts
...
/var/www/html/out52499.ts
```

1これを `rm` に渡せれたら先頭50個を削除できそうだ。

### 100個以上の時に削除するシェルスクリプト

で、こうなった。

```sh
#!/bin/sh

FILECOUNT=$(ls /var/www/html/ | grep -E "*\.ts" | wc -l)

if test $FILECOUNT -gt 100 ; then
  ls /var/www/html/ | grep -E "*\.ts" | sort -t 't' -k 2n,2 | head -50 | sed -e 's:^:/var/www/html/:g' | xargs rm
fi
```

仮で _remove_old.sh_ とする。

これを任意のディレクトリに置いて、 `cron` で定期実行させる。例えば5分ごとの場合

```sh
sudo crontab -u root -e
...
*/5 * * * * /path/to/shdir/remove_old.sh
```

という設定を書き `cron` を再起動させる。


{% capture text %}

### Start/Stop/Restart cron service in Debian/Ubuntu

If you are using Debian/Ubuntu Linux login as root and use the following commands.

**Start cron service**

To start cron service, enter:

```sh
$ sudo /etc/init.d/cron start
```

Or

```sh
$ sudo service cron start
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[How To Start/Stop/Restart Cron Service In Linux](http://lifelinux.com/how-to-startstoprestart-cron-service-in-linux/)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

```sh
$ sudo service cron restart
```

5分毎に動作しているかを _/var/log/syslog_ で確認する。

```sh
$ sudo cat /var/log/syslog | tail
Apr 30 17:10:01 crontestuser CRON[25948]: (root) CMD (/path/to/shdir/remove_old.sh)
Apr 30 17:15:01 crontestuser CRON[26751]: (root) CMD (/path/to/shdir/remove_old.sh)
Apr 30 17:20:02 crontestuser CRON[27510]: (root) CMD (/path/to/shdir/remove_old.sh)
Apr 30 17:25:01 crontestuser CRON[28318]: (root) CMD (/path/to/shdir/remove_old.sh)
```

よさそう。
