# SanshineCTF2025　Space Is My Banner
## 問題文
I did it again.

This time I'm sure I accessed a satellite.

I'm scared, it's giving me a warning message when I log in.

I think this time I may have gone too far... this seems to be some top security stuff...

socat file:`tty`,raw,echo=0 TCP:chal.sunshinectf.games:省略

またやってしまった。

今回は間違いなく衛星にアクセスした。

怖い、ログインすると警告メッセージが出るんだ。

今回はやりすぎたかもしれない…どうやら最高機密レベルのものらしい…

socat file:`tty`,raw,echo=0 TCP:chal.sunshinectf.games:省略

## 解法
とりあえずアクセスしてみると、tmuxというCUI上でGUI操作できるやつにアクセスできる。
tmuxの機能で好きなコマンドを実行できるので、それを使う。
```
Ctrl + b
```
の後に：（コロン）を入力するとtmuxのコマンドを入力できる。
```
set default-shell /bin/sh
split-window
```
これでコンソールが表示される。
あとはls -laしてcat-flagという実行ファイルがあるので実行するだけ
```
$ ls -al
total 80
dr-xr-xr-x    1 root     root          4096 Sep 27 14:22 .
drwxr-xr-x    1 root     root          4096 Sep 27 16:31 ..
-r-xr-sr-x    1 root     flag-read     18512 Sep 27 14:22 cat-flag
-r-xr-xr-x    1 root     root          5213 Sep 23 20:52 challenge.sh
-r-xr-x---    1 root     root         18592 Sep 27 14:22 drop-perms
-r-xr-xr-x    1 root     root           367 Sep 23 20:52 fake-term.sh
-r--r-----    1 root     flag-read        82 Sep 23 20:52 flag.txt
-r--r--r--    1 root     root          5619 Sep 23 20:52 system_logs.txt
$ ./cat-flag
sun{省略}
```
余談
非常にくやしい　ぐぐり力もっとあったら解けたかも