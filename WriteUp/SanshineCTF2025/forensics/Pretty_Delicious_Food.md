## Pretty Delicious Food
### 問題文
```
This cake is out of this world! :DDDDDDD

omnomonmonmonmonm

...

something else is out of place too.

Note: This is not a steganography challenge
```
### 添付ファイル
`prettydeliciouscakes.pdf`
### 解き方
1. `ptdfk` でPDFファイルの圧縮を解除する
```sh
pdftk prettydeliciouscakes.pdf output pretty_uncompressed.pdf uncompress
```
2. 圧縮を解除したPDFの中身をstringsで解析する
```sh
strings pretty_uncompressed.pdf
```

3. 上から順に見て行くと以下のような記述があるのでBase64で復号する
```java
const data = 'c3Vue3AzM3BfZDFzX2ZsQGdfeTAhfQ==';
```
flag
```
sun{p33p_d1s_fl@g_y0!}
```