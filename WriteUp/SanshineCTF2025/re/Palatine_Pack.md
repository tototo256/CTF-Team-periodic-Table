## Palatine Pack
### 問題文
```
Caesar is raiding the Roman treasury to pay off his debts to his Gallic allies, and to you and his army. Help him find the password to make this Lucius Caecilius Metellus guy give up the money! >:) (he is sacrosanct so no violence!)

currently has nonstandard flag format sunshine{}
```
### 添付ファイル
`palatinepack` `flag.txt`
### 解き方
1. palatinepackを`objdump`で逆アセンブルする。
```sh
objdump -d palatinepack
```
2. chatGPTに逆アセンブルした結果と添付ファイルを渡すと解いてくれた。
```
sunshine{C3A5ER_CR055ED_TH3_RUB1C0N}
```
### 参考
https://chatgpt.com/share/68dbd6b5-525c-800e-9104-bcadc9606448