## omikuji
### 問題文
```
カフェドマンシー

ヒント：フラグはappサーバの/flagにあります。配布ファイルのapp/server.jsをよく読んでみましょう。

http://34.170.146.252:40060
```
### 添付ファイル
`omikuji.tar.gz`
### 解き方
1. tar.gzを解凍し，server.jsを読むと，saveエンドポイントにPOSTリクエストを送ると送ったテキストがそのままファイルパスとして使われ，
送ったパスのファイルを読み取り，result/に保存していることが分かる．
2. よって，flagへのパスをPOSTリクエストで送ればよいことが分かる。
3. curl -X POST -d "../flag" http://34.170.146.252:40060/save
4. こうすることで，{"location":"/result/df7cb960.html"}というテキストが返ってくる．
5. これは保存されたパスを示しているので，http://34.170.146.252:40060/result/df7cb960.htmlにアクセスすることでフラグを入手できる
6. フラグ TSGLIVE{1_knew_at_f1rst_g1ance_that_1t_was_so_0rdin4ry_path_traversal}