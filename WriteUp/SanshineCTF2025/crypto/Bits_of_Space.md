## Bits of Space
### 問題文
 ```
Bits of Space
Here is a code to one of our relays, can you reach the others?

nc sunshinectf.games 25401
```
### 添付ファイル
`voyager.bin` `relay.py`

### 解き方
1. relay.pyを見ると、AES暗号をCBC (Cipher Block Chaining) モードで利用していることがわかり、CBCビットフリッピングの脆弱性があることがわかる。

2. エクスプロイトコード`solve.py`を作成する。Geminiありがとう。

3. 実行してflagを入手する
```
% python solve.py 

sunshinectf.games:25401 に接続しています...
== Space Relay ==
Send your subscription packet:
細工したパケットを送信します...

サーバーからの応答:
You have reached the restricted relay... here you go.
sun{m4yb3_4_ch3ck5um_w0uld_b3_m0r3_53cur3}
See you next time space cowboy.
```
### 参考
https://g.co/gemini/share/9c6c05903c97
https://qiita.com/no0m/items/36ecdcbeb392b26399e4