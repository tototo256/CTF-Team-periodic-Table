# picoCTF2025　Quantum Scrambler

```
import sys

def exit():
  sys.exit(0)

def scramble(L):
  A = L
  i = 2
  while (i < len(A)):
    A[i-2] += A.pop(i-1)
    A[i-1].append(A[:i-2])
    i += 1
    
  return L

def get_flag():
  flag = open('flag.txt', 'r').read()
  flag = flag.strip()
  hex_flag = []
  for c in flag:
    hex_flag.append([str(hex(ord(c)))])

  return hex_flag

def main():
  flag = get_flag()
  cypher = scramble(flag)
  print(cypher)

if __name__ == '__main__':
  main()
```

とりあえずnc接続して出力
```
nc verbal-sleep.picoctf.net 52680 > output.txt
```
A = L でリストの参照をコピー
i = 2 から開始して、len(A) まで繰り返し
以下ループ

A[i-2] += A.pop(i-1): インデックスi-1の要素を取り出して、i-2の要素に追加
A[i-1].append(A[:i-2]): 新しいi-1番目の要素に、先頭からi-2までの要素を追加
これの逆順をする

元の処理は前から後ろに向かって要素を操作するので、逆変換は後ろから前に向かって処理
各ステップで追加された要素を削除し、取り出された要素を元の位置に戻す

ClaudeAIに任せたらsolverを作ってくれた
chatGPTとGeminiやったらだめだった　優秀なんかな
```
import copy

def unscramble(scrambled_data):
    A = copy.deepcopy(scrambled_data)
    i = len(A) - 1
    while i >= 2:
        if len(A[i-1]) > 1:
            A[i-1].pop()
        if len(A[i-2]) > 1:
            original_element = [A[i-2].pop()]
            A.insert(i-1, original_element)
        i -= 1
    return A

def hex_to_flag(hex_list):
    flag = ""
    for hex_char in hex_list:
        char_code = int(hex_char[0], 16)
        flag += chr(char_code)
    return flag

def solve():
    with open('output.txt', 'r') as f:
        scrambled_data = eval(f.read().strip())
    
    original_data = unscramble(scrambled_data)
    flag = hex_to_flag(original_data)
    print(flag)
    return flag

if __name__ == '__main__':
    solve()
```