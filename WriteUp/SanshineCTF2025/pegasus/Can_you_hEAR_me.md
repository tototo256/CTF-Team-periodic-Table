## Can you hEAR me?
### 問題文
```
A satellite has fallen from orbit, and miraculously, it is still operational. It appears to be running on a RAD-EAR-3 CPU, which is known for its reliability in space applications. The satellite has a handful of program cartridges that it can swap between (like a jukebox), and one of them is labeled "hello". Can you figure out how to run it? We need the satellite's serial number!

How to run a PEGASUS file:

runpeg <file.peg> [--debug] [--verbose] [--trace]

There are more options you can find with runpeg --help :)
```
### 添付ファイル
- PEGASUS file for this challenge
`CanYouHearMe.peg`
- Core PEGASUS v3 files:
`runpeg`
`libear.so`
`libeardbg.so`
- Documentation for v3:
`EAR_EAR_v3.md`
`PEGASUS.md`

### 解き方
1. runpegのhelpを確認し、--uartというオプション(ポート0xDに書き込まれた出力を表示する)があるのでこれを使って実行するとフラグを得られる。
```sh
./runpeg CanYouHearMe.peg --uart
```
### 参考
https://g.co/gemini/share/106822050bf3

### その後の問題について

おそらくgdb使えば
```
gdb ./runpeg CanYouHearMe.peg --uart
```
で`CanYouHearMe.peg`の内容を逆アセンブルできる。それで解くらしい

アセンブリ読めないとやっぱり駄目か