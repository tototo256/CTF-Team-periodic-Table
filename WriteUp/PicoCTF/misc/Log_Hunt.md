## Log Hunt
### 問題文
```
Our server seems to be leaking pieces of a secret flag in its logs. The parts are scattered and sometimes repeated. Can you reconstruct the original flag?
Download the logs and figure out the full flag from the fragments.
```
### 添付ファイル
`server.log`
### 解き方
1. server.logを見ると、長いログが出力される
2. 一番上を見ると下のように出力されている
```bash
[1990-08-09 10:00:10] INFO FLAGPART: picoCTF{us3_
```
3. ここから、フラグはINFO FLAGPARTで出力されていると推測できるので下のようにgrepする
```bash
grep "INFO FLAGPART" server.log
```
4. いくつかのパターンが出力されるので、結合してフラグを作る
5. フラグ picoCTF{us3_y0urlinux_sk1lls_cedfa5fb}