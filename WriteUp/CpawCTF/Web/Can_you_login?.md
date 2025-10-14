## Q28.[Network] Can you login？
### 問題文
```
古くから存在するネットワークプロトコルを使った通信では、セキュリティを意識していなかったこともあり、様々な情報が暗号化されていないことが多い。そのため、パケットキャプチャをすることでその情報が簡単に見られてしまう可能性がある。
次のパケットを読んで、FLAGを探せ！
```
### 添付ファイル
`network100_be557d01b0299a03dd3569893226dda424efc9a0.pcap`
### 解き方
1. wiresharkで開く
2. ftpでq28.ctf.cpaw.siteにアクセスしていることが分かり，usernameやpasswordも書いてあるのでログインする
3. ls -laとすると.hidden_flag_fileがあるのでそれを保存する
4. .hidden_flag_fileにフラグが書いてある
5. フラグ cpaw{f4p_sh0u1d_b3_us3d_in_3ncryp4i0n}