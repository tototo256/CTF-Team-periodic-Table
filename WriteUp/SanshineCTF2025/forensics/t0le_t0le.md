## t0le t0le
### 問題文
```
Our CCDC business guy made a really weird inject. He's just obsessed with that damn cat... there's nothing hiding in there, right?
```
### 添付ファイル
`Team_5_-_Inject_72725.docx`

### 解き方
1. Office系列のファイルはzipのような複数ファイルをまとめた圧縮ファイルなので`binwalk` で展開する。
```sh
binwalk -e Team_5_-_Inject_72725.docx
```
2. 展開したファイルの中から`oleObject1.bin` を探し、stringsで内容を確認する。
```sh
strings oleObject1.bin 
```
出力
```sh
OLE Package
Package
C:\Users\ardy\Downloads\vro
C:\Users\ardy\AppData\Local\Temp\{016E985C-CACE-4FD5-BE62-7088A89D6E7F}\{07D47D53-4E54-46B2-9C7A-31322E56717A}\vro
Zmhhe2cweXJfZzB5cl96bF9vM3kwaTNxIX0=
```
3. 出力に明らかにBase64文字列の`Zmhhe2cweXJfZzB5cl96bF9vM3kwaTNxIX0=`があるので復号すると
`fha{g0yr_g0yr_zl_o3y0i3q!}` が得られる。

4. どう見てもシーザー暗号なのでROT13で復号するとflagを得られる。
```
sun{t0le_t0le_my_b3l0v3d!}
```
### 補足
フラグ形式の文字 (今回ならsun)のROT13は出題された時にすぐに気づくために先に調べておくのがおすすめ