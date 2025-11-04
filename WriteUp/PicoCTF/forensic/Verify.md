## Verify
### 問題文
```
People keep trying to trick my players with imitation flags. I want to make sure they get the real thing! I'm going to provide the SHA-256 hash and a decrypt script to help you know that my flags are legitimate.
Additional details will be available after launching your challenge instance
```
### 解き方
1. filesディレクトリに多数のファイルがあるのでそのどれが与えられたchecksumと一致するか調べればよい
```bash
find files -type f -print0 | xargs -0 sha256sum | grep -i '^fba9f49bf22aa7188a155768ab0dfdc1f9b86c47976cd0f7c9003af2e20598f7'
```
2. これを実行するとfiles/87590c24とでるので./decrypt.sh files/87590c24を実行するとフラグが出力される
3. フラグ picoCTF{trust_but_verify_87590c24}