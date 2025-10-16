## Hidden in plainsight
### 問題文
```
You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag.
Download the jpg image here.
```
### 添付ファイル
`img.jpg`
### 解き方
1. fileコマンドを使うと怪しげなcommentが出力される
2. とりあえずbase64でデコードすると、steghide:cEF6endvcmQ=が出力される
3. コロン以降をもう一度base64でデコードすると、pAzzwordが出力される
4. よってこれはsteghideを使えというヒントであるため以下のコマンドを実行する
```bash
steghide extract -sf img.jpg -p pAzzword
```
5. flag.txtが出力されるため、中身を確認するとフラグを入手できる
6. フラグ picoCTF{h1dd3n_1n_1m4g3_e7f5b969}