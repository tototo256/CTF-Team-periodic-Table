## Cookie Monster Secret Recipe
### 問題文
```
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe?
Additional details will be available after launching your challenge instance.
```
### 解き方
1. usernameとpasswordに適当な文字列を入れてloginする
2. DevToolsを開き，Application/Storage/Cookiesからsecret_recipeを取得する
3. secret_recipe cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzJDODA0MEVGfQ%3D%3D
4. %3D%3Dはパーセントエンコードされており，デコードするとこれは==となる
5. よってこれはbase64エンコードされていると判断でき，デコードするとフラグを取得できる
6. フラグ picoCTF{c00k1e_m0nster_l0ves_c00kies_2C8040EF}