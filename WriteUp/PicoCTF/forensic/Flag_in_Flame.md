## Flag in Flame
### 問題文
```
The SOC team discovered a suspiciously large log file after a recent breach. When they opened it, they found an enormous block of encoded text instead of typical logs. Could there be something hidden within? Your mission is to inspect the resulting file and reveal the real purpose of it. The team is relying on your skills to uncover any concealed information within this unusual log.
Download the encoded data here: Logs Data. Be prepared—the file is large, and examining it thoroughly is crucial .
```
### 添付ファイル
`logs.txt`
### 解き方
1. 添付ファイルをbase64でデコードし，画像ファイルとして出力する
2. 画像に長い数字が書いてある．
3. 16進数と推測できるのでasciiに変換するとフラグを入手できる
4. フラグ picoCTF{forensics_analysis_is_amazing_c75dd08e}