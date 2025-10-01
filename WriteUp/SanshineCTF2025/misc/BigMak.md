## BigMak
### 問題文
```
I tried typing out the flag for you, but our Astronaut Coleson seems to have changed the terminal's keyboard layout? He went out to get a big mak so I guess we're screwed. Whatever, here's the flag, if you can somehow get it back to normal.

rlk{blpdfp_iajylg_iyi}
```
### 解き方
1. とりあえず見た感じ単一換字式暗号なのでflag形式よりr=s,l=u,k=nと仮定する。(これ定石)

2. 問題文より1の対応に合うキー配列が存在しそうなのでAIに聞いてみるとColeMak配列(https://ja.wikipedia.org/wiki/Colemak%E9%85%8D%E5%88%97)
というのがあるらしいのでこれに当てはめて復号する。

```
sun{burger_layout_lol}
```