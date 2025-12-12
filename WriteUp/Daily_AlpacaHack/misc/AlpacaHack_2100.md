## AlpacaHack 2100
### 問題文
```
🦙 < フラグは Daily AlpacaHack の 2100年1月 のカレンダーにあるパカ
```
### 解き方
1. サイト下部にカレンダーがありボタンを押すことで前後の月に遷移できる．
2. 2100年まで1000回ほどボタンを押せばフラグを取得できそうだがめんどくさい
3. 次の月のカレンダーに遷移し，urlを確認するとhttps://alpacahack.com/daily?month=2026-01となっており，年月をurlで指定できることが分かる．
4. よってurlをhttps://alpacahack.com/daily?month=2100-01 とすることで2100年のカレンダーを確認することが出来る．
5. 2100年1月のカレンダーを確認すると11日から17日にかけてフラグが書いてあるので，つなげることでフラグを取得できる．
6. フラグ：Alpaca{brought_AGI_to_humanity..._yes,_Alpaca_Gentle_Intelligence.}