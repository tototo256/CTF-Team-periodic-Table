## Lunar Auth
### 問題文
```
Infiltrate the LunarAuth admin panel and gain access to the super secret FLAG artifact !

https://comet.sunshinectf.games
```
### 解き方
1. URLにアクセスすると以下の文字列が目につく
```We have amazing auth, no robots are allowed — only admins may dock. Solve the challenge and gain access to the admin portal.
```

2. adminにアクセスしたいので/adminにアクセスしてみる。

3. /adminにはログインフォームが用意されているのでとりあえず管理者画面でソースを確認する。(script部のみ抜粋)
```js

    /*
    To reduce load on our servers from the recent space DDOS-ers we have lowered login attempts by using Base64 encoded encryption
    ("encryption" :skull:) on the client side.
    
    TODO: implement proper encryption.
    */
    const real_username = atob("YWxpbXVoYW1tYWRzZWN1cmVk");
    const real_passwd   = atob("UzNjdXI0X1BAJCR3MFJEIQ==");

    document.addEventListener("DOMContentLoaded", () => {
        const form = document.querySelector("form");

        function handleSubmit(evt) {
        evt.preventDefault();

        const username = form.elements["username"].value;
        const password = form.elements["password"].value;

        if (username === real_username && password === real_passwd) {
            // remove this handler and allow form submission
            form.removeEventListener("submit", handleSubmit);
            form.submit();
        } else {
            alert("[ Invalid credentials ]");
        }
        }

        form.addEventListener("submit", handleSubmit);
    });
    
```
4. scriptにBase64エンコードされたユーザーネームとパスワードが書いてあるので復号すると、
```
user_name: alimuhammadsecured
password : S3cur4_P@$$w0RD!
```
ということがわかるので、これでログインするとフラグを得ることができる。
```
sun{cl1ent_s1d3_auth_1s_N3V3R_a_g00d_1d3A_983765367890393232}
```

### 補足
robots.txtにアクセスすると以下の記述があるので、/adminというページがあることがわかる。
```
# tired of these annoying search engine bots scraping the admin panel page logins:

Disallow: /admin
```