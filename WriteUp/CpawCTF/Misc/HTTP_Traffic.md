## Q16.[Network+Forensic]HTTP Traffic
### 問題文
```
HTTPはWebページを閲覧する時に使われるネットワークプロトコルである。
ここに、とあるWebページを見た時のパケットキャプチャファイルがある。
このファイルから、見ていたページを復元して欲しい。
```
### 添付ファイル
`http_traffic.pcap`
### 解き方
1. Wiresharkで添付ファイルを開く
2. httpで絞る
3. htmlが見つかるのでそれを読む
4. フラグを取得するにはボタンを押せばよく，ボタンに関することはbutton.jsに実装されていると分かる
5. ファイル/オブジェクトをエクスポート/HTTPからhtmlとjavascriptファイルを取得する
6. htmlを開いてボタンを押せばフラグが表示される．
7. ボタンが押せない場合，htmlのjavascriptへのパスを確認する．
8. フラグ cpaw{Y0u_r3st0r3d_7his_p4ge}