## Q21.[Reversing]reversing easy!
### 問題文
```
フラグを出す実行ファイルがあるのだが、プログラム(elfファイル)作成者が出力する関数を書き忘れてしまったらしい…
```
### 添付ファイル
`rev100`
### 解き方
1. objdumpで添付ファイルを逆アセンブルする
2. mainの部分を読み，フラグの書いてありそうなmovされている値をhexからasciiへ変換する．
3. cpaw{yakiniku!}と変換できる
4. フラグ cpaw{yakiniku!}