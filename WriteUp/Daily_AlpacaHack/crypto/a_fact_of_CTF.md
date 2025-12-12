## a fact of CTF
### 問題文
```
AlpacaHack で一番最初に完成したものの難易度調整により出題されなかった幻の問題 （運営コメント）
```
### 添付ファイル
`a-fact-of-CTF.tar.gz`
### 解き方
1. tar.gzを解凍する
```bash
tar -xzf a-fact-of-CTF.tar.gz
```
2. ファイルを確認するとchall.pyとoutput.txtというものがあるのが分かる．
3. chall.pyはflagを一文字ずつ異なる素数をその文字のUnicode値乗したものの総積を出力するプログラムである
4. output.txtはchall.pyの出力結果であると推測できる
5. 素因数分解は一意であるためこれは復元でき，復元するとフラグが取得できる
```python
ct = int(open("output.txt").read().strip(), 16)
primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103, 107, 109, 113, 127, 131, 137, 139, 149, 151, 157, 163, 167, 173, 179, 181, 191, 193, 197, 199, 211, 223, 227, 229, 233, 239, 241, 251, 257, 263, 269, 271, 277, 281, 283, 293]
exps = []
for p in primes:
    e = 0
    while ct % p == 0:
        ct //= p
        e += 1
    exps.append(e)
msg = ''.join(chr(e) for e in exps if e != 0)
print(msg)
```
6. フラグ：Alpaca{prime_factorization_solves_everything}