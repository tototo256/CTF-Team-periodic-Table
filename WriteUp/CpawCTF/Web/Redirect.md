## Q15.[Web] Redirect
### 問題文
```
このURLにアクセスすると、他のページにリダイレクトされてしまうらしい。
果たしてリダイレクトはどのようにされているのだろうか…
http://q15.ctf.cpaw.site
```

### 解き方
1. urlにアクセスすると，http://q9.ctf.cpaw.site/ にリダイレクトされる．
2. 開発者ツールを開いた状態でもう一度問題文のurlにアクセスする
3. 開発者ツールのNetworkからq15.ctf.cpaw.siteを見るとResponse HeadersのX-Flagにフラグが書いてある．
4. フラグ cpaw{4re_y0u_1ook1ng_http_h3ader?}