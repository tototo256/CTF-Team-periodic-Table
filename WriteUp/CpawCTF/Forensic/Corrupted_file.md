## Corrupted file
### 問題文
```
This file seems broken... or is it? Maybe a couple of bytes could make all the difference. Can you figure out how to bring it back to life?
Download the file here.
```
### 添付ファイル
`file`
### 解き方
1. 問題文からバイナリが破損していると分かるので，バイナリエディタで添付ファイルを開く．
2. J F I F(4A 46 49 46)とあるのでこれはjpegファイルであると推測できる
3. jpegファイルは先頭がFF D8 FF E0である必要があるので，バイナリエディタで編集する
4. 編集したファイルをjpegで開くとフラグが書いてある
5. フラグ picoCTF{r3st0r1ng_th3_by73s_249e4e3c}
### 参考
https://cyber-security-forensic.com/cyberfilename_extension/