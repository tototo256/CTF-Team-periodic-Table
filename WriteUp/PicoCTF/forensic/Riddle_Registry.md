## Riddle Registry
### 問題文
```
Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered.
Find the PDF file here Hidden Confidential Document and uncover the flag within the metadata.
```
### 添付ファイル
`confidential.pdf`
### 解き方
1. メタデータを取得しろと問題文に書いてあるので下のコマンドで取得する
```bash
$pdfmetadata confidential.pdf
```
2. 以下が出力される
```bash
[*] Document information dictionary:
Producer            : PyPDF2
Author              : cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9lZTQ1NDk1MH0=
```
3. AuthorがBase64エンコードっぽいのでデコードするとフラグが入手できる
4. フラグ picoCTF{puzzl3d_m3tadata_f0und!_ee454950}
### 参考
https://www.base64decode.org/