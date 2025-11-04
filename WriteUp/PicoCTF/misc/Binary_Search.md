## Binary Search
### 問題文
```
Want to play a game? As you use more of the shell, you might be interested in how they work! Binary search is a classic algorithm used to quickly find an item in a sorted list. Can you find the flag? You'll have 1000 possibilities and only 10 guesses.
Cyber security often has a huge amount of data to look through - from logs, vulnerability reports, and forensics. Practicing the fundamentals manually might help you in the future when you have to write your own tools!
You can download the challenge files here:
challenge.zip
Additional details will be available after launching your challenge instance.
```
### 添付ファイル
`challenge.zip`
### 解き方
1. 2分探索をする．
2. 初期値はlow=1, high=1000でmid=(low + high) / 2とし，midを入力するとmidより大きいか小さいかが出力される．
3. Higherと表示された場合midより大きいのでlow=midとし繰り返す．Lowerと表示された場合midより小さいのでhigh=midとし繰り返す．
4. 正解の値を入力するとフラグが表示される
5. フラグ picoCTF{g00d_gu355_2e90d29b}