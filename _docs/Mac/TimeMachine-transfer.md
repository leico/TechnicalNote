---
layout : post
title  : Time Machine 用 HDD を移行しようとして時間を無駄にしつつ解決するまで
date   : 2020/01/12
lastchange : 2020-01-13 00:30:06.
tags   :
  - Mac
  - Macbook
  - OSX
  - macOS
  - Mojave
  - Time Machine
---

## 移行が終わらないし容量がおかしい

Time Machine 用ディスクの移行方法は Apple のサイト
[別のバックアップディスクに Time Machine バックアップを転送する方法 - Apple サポート](https://support.apple.com/ja-jp/HT202380)
に詳しく載っています。しかし


{% capture text %}

## 新しいディスクにバックアップデータをコピーする

1. Finder で、それぞれのバックアップディスクの新しいウインドウを開き、ウインドウ間でファイルをドラッグできるようにします。
2. 古いバックアップディスクの最上位に、次のどちらかが表示されます (お使いのバックアップディスクの種類によって異なります)。
    * 末尾が「.sparsebundle」のファイル
    * 「Backups.backupdb」という名前のフォルダ
            
        _「Backups.backupdb」フォルダが表示される場合は、Mac に直接接続した外付けの USB ドライブ、Thunderbolt ドライブ、
        または FireWire ドライブを新しいバックアップディスクとして使う場合にのみ、バックアップを転送することができます。
        [Time Machine で使えるディスク](https://support.apple.com/ja-jp/HT202784)
        でも別の種類のディスクの場合は、バックアップを転送できません。_

3. 古いバックアップディスクの「.sparsebundle」ファイルを新しいバックアップディスクの最上位にドラッグします。
4. (入力を求められた場合は) 管理者の名前とパスワードを入力し、コピーが完了するまで待ちます。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[別のバックアップディスクに Time Machine バックアップを転送する方法 - Apple サポート](https://support.apple.com/ja-jp/HT202380)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}



この部分の _2. - 3._ の部分が延々と終わりませんでした。



{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/01_endless.png %}{% endcapture %}
{% capture caption %}
延々終わらず、HDD 容量をオーバーする量が転送されています
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


公式の方法ではうまくいかないので、

* 原因
* 試してだめだった方法
* 成功して解決するまで

をまとめました。


参考:

* [別のバックアップディスクに Time Machine バックアップを転送する方法 - Apple サポート](https://support.apple.com/ja-jp/HT202380)
* [manに「cp -rは使うな」と書いてあった話 - 西尾泰和のはてなダイアリー](https://nishiohirokazu.hatenadiary.org/entry/20120718/1342594795)
* [macOS Mojave の &quot;Operation not permitted&quot; を回避する（du/ls/mv/cp 実行時） - Qiita](https://qiita.com/KEINOS/items/0366f1c281b574a79cfb)
* [ddコマンドのbsサイズ - Qiita](https://qiita.com/wf9a5m75/items/6bfea7a2b8c9658ce4fc)





## シンボリックリンクのリンク先がコピーされている

ありえない容量がコピーされている原因は、バックアップのディレクトリ構成内でシンボリックリンクが貼られている = 
以前のバックアップから変化していないファイルが多重にコピーされているのが問題のようでした。
つまり、シンボリックリンクがシンボリックリンクとしてコピーされてくれさえすればこの問題は解決します。
この時点で、普通にドラッグ & ドロップではコピーできない = 公式の方法では移行できないことが判明します。




### シンボリックリンクをそのままコピーする

{% capture text %}

`cp -r` でシンボリックリンクまで実体としてコピーされて困ったので Mac の man を読んでいたのだが(中略)

---

代わりに `-R` を使うべきだそうだ。その場合のシンボリックリンクの扱いをどうするかはオプションで指定できて、デフォルトでは _no symbolic links are followed_ 。

```
-P If the -R option is specified, no symbolic links are followed. This is the default.
```

---

#### 追記(他のやり方など)

* `cp -a` 派
    * ただしこれが実装されているかは環境依存(hiby: `-a` オプションが実装されてない UNIX OSも在るのですよ…)

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[manに「cp -rは使うな」と書いてあった話 - 西尾泰和のはてなダイアリー](https://nishiohirokazu.hatenadiary.org/entry/20120718/1342594795)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


`cp -a` のヘルプを参照してみます。

```man
  -a    Same as -pPR options. Preserves structure and attributes of
        files but not directory structure.
```

```man
  -p    Cause cp to preserve the following attributes of each source
        file in the copy: modification time, access time, file flags,
        file mode, user ID, and group ID, as allowed by permissions.
        Access Control Lists (ACLs) and Extended Attributes (EAs),
        including resource forks, will also be preserved.
```

```man
  -P    If the -R option is specified, no symbolic links are followed.
        This is the default.
```

```man
  -R    If source_file designates a directory, cp copies the directory
        and the entire subtree connected at that point.  If the
        source_file ends in a /, the contents of the directory are
        copied rather than the directory itself.  This option also
        causes symbolic links to be copied, rather than indirected
        through, and for cp to create special files rather than copy-
        ing them as normal files.  Created directories have the same
        mode as the corresponding source directory, unmodified by the
        process' umask.
```

`-a` の説明どおり、アクセス権、ファイル日時等を全て保った状態でコピーする。シンボリックリンクもそのままで追跡しないようです。


### `cp -a` が実行できない

と、いうことで `cp -a` を試しましたが、 `sudo` しても権限がないと言われてしまいます。

```
$ sudo cp -av /Volumes/Backup_late2016/Backups.backupdb /Volumes/Backup_late2016\ 1/.
Password:
cp: /Volumes/Backup_late2016/Backups.backupdb: unable to copy extended attributes to /Volumes/Backup_late2016 1/./Backups.backupdb: Operation not permitted
/Volumes/Backup_late2016/Backups.backupdb -> /Volumes/Backup_late2016 1/./Backups.backupdb
cp: /Volumes/Backup_late2016/Backups.backupdb: Operation not permitted
```

{% capture text %}

> `[システム環境設定]` - `[セキュリティとプライバシー]` - `[プライバシー]タブ` - 
> `（左ペインの）[フルディスクアクセス]` -`（左下の鍵を解除。要パスワード）` - `「＋」ボタン押下` - `「ターミナル」を追加`

---

macOS **Mojave（OSX 10.14）以降でセキュリティ設定が厳しくなったため** 、`sudo` 付きやユーザーのホームディレクトリ下でも
「 `Operation not permitted` 」が出るようになりました。これは、 `du` `ls` `mv` `cp` といった基本コマンドでも同じです。

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[macOS Mojave の &quot;Operation not permitted&quot; を回避する（du/ls/mv/cp 実行時） - Qiita](https://qiita.com/KEINOS/items/0366f1c281b574a79cfb)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


ここに挙がっている通りで、 Time Machine 用のファイルをターミナルから操作する際は _フルディスクアクセス_ の権限がターミナルに必要なようです。





{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/02_fulldisk_access.png %}{% endcapture %}
{% capture caption %}
フルディスクアクセス権限を付与してターミナルを再起動させます。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




これをしたら無事にコピーがスタートしました。が、すごく時間がかかっていました。しばらく放置していたのですが、画像のところで HDD がスリープするほど長時間止まっていました。これはよくありません。




{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/03_freeze.png %}{% endcapture %}
{% capture caption %}
あまりにも長時間ストップしているので中止しました。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




別の方法をかんがえます。




## `dd` 最強

こうなったら `dd` コマンドで HDD ごとコピーします。完了後にディスクユーティリティでパーティションを 1TB に拡張させることで移行を試みました。
これが正解でした。

まずはハードディスクが `/dev/` 以下のどこに割り当てられているかディスクユーティリティで確認します。



{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/04_dev_check.png %}{% endcapture %}
{% capture caption %}
`disk2` の表記が見えるので、この HDD の場合 `/dev/disk2` に割り当てられています。
`dd` なので `disk*` の先頭に `r` をつけて `/dev/rdisk*` 、シーケンシャルにしても問題ありません。
ランダムアクセスじゃない分速度があがります。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}



`disk*` を確認したらマウントを解除して、間違えないように書き込みます。


{% capture text %}

私のMac Miniではこんな感じでした。いつも `bs=32m` で指定していましたが、 `bs=16m` のが早いわけですね。

```bash
$> ./test_dd_bs.sh 

creating a file to work with
1175000+0 records in
1175000+0 records out
601600000 bytes transferred in 3.716253 secs (161883487 bytes/sec)
---------------------------------------
Testing block size  = 16m
35+1 records in
35+1 records out
601600000 bytes transferred in 1.312157 secs (458481751 bytes/sec)

---------------------------------------
Testing block size  = 32m
17+1 records in
17+1 records out
601600000 bytes transferred in 1.512825 secs (397666614 bytes/sec)

---------------------------------------
Testing block size  = 64m
8+1 records in
8+1 records out
601600000 bytes transferred in 1.472201 secs (408639823 bytes/sec)

---------------------------------------
Testing block size  = 128m
4+1 records in
4+1 records out
601600000 bytes transferred in 1.510589 secs (398255218 bytes/sec)

---------------------------------------
Testing block size  = 256m
2+1 records in
2+1 records out
601600000 bytes transferred in 1.574169 secs (382169855 bytes/sec)

---------------------------------------
Testing block size  = 512m
1+1 records in
1+1 records out
601600000 bytes transferred in 1.684105 secs (357222349 bytes/sec)
```


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[ddコマンドのbsサイズ - Qiita](https://qiita.com/wf9a5m75/items/6bfea7a2b8c9658ce4fc)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}





自分の Mac で計っていないですが、これに従ってブロックサイズを設定しました。通常より早いような気がします。




```sh
$ sudo dd if=/dev/rdisk3 of=/dev/rdisk2 bs=16m
Password:
44712+1 records in
44712+1 records out
750156374016 bytes transferred in 10548.198580 secs (71117013 bytes/sec)
```




この `dd` を行う際にも _フルディスクアクセス_ 権限が必要でした。移行先の HDD をマウントすると前のドライブ同様、
Time Machine に入ることができます。




{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/05_enter.png %}{% endcapture %}
{% capture caption %}
Time Machine に入れました。
{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}




これが確認できたら容量に合わせてパーティションを拡張します。
ディスクユーティリティのパーティションから空き領域を消去します。




{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/06_delete_empty.png %}{% endcapture %}
{% capture caption %}

1. 空き領域を選択し
2. 円グラフ下部の `-` をクリックして消去
3. 右下の適用でパーティションが拡大される


{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}





もしエラーが出た場合は First Aid を試してみましょう。パーティションの拡張が終わった後も一度かけておくのがおすすめです。




{% capture url %}{{site.github.url}}{% link _docs/Mac/images/TimeMachine-transfer/07_first_aid.png %}{% endcapture %}
{% capture caption %}
なにか起きたらまずは First Aid でエラーチェックを

{% endcapture %}
{% assign caption = caption | markdownify %}
{% include thumbnail.html url=url caption=caption %}


ここまでで移行が終了しました。長かった。
