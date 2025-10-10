## Q18.[Forensic]leaf in forest
### 問題文
```
このファイルの中にはフラグがあります。探してください。
フラグはすべて小文字です！
```
### 添付ファイル
`misc100`
### 解き方
1. fileコマンドを使うとpcapと出たがwiresharkで開けなかったのでpcapではない
2. stringsコマンドを使うと大量のlovelive!の文字と，それ以外がたまに入る文章が出力される．
3. tr -d 'lovelive!' < misc100とすると�ò���CCCPPPAAAWWW{{{MMMGGGRRREEEPPP}}}が出力される
4. 3重になっている文字を修正するとフラグになる
5. フラグ cpaw{mgrep}