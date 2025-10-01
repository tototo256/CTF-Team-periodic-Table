# picoCTF2025　Tap into Hash

claudeにぶん投げたらsolver作ってくれた
```
(プログラム省略)
このプログラムを解析して、逆順にするプログラムを作成してください。
Key: (略)
Encrypted Blockchain:  (略)
```
```
このプログラムを解析して、暗号化されたブロックチェーンを復号化するプログラムを作成します。
まず、プログラムの処理フローを理解しましょう：

ブロックチェーンを作成（ジェネシスブロック + 4つの追加ブロック）
各ブロックのハッシュを計算してハイフンで結合した文字列を作成
その文字列の中央にtokenを挿入
XOR暗号化で暗号化

逆順処理を行うプログラムを作成します：
このプログラムは元のプログラムの処理を逆順で実行します：
主要な処理：

復号化 (decrypt関数)：

XOR暗号化を逆順で実行
同じキーでXOR演算を再度行うことで元のデータを復元
パディングを除去


トークン抽出 (extract_token_from_decrypted_data関数)：

復号化されたデータから、元々中央に挿入されたトークンを抽出
ブロックチェーン文字列の期待される長さ（324文字）を基に計算


ブロックチェーン文字列の復元：

トークンを除去して元のブロックチェーン文字列を復元
ハイフンで分割して各ブロックのハッシュを表示



期待される結果：

抽出されたトークンが、元のプログラムに渡されたsys.argv[1]の値
5個のブロックハッシュが表示される（ジェネシスブロック + 4個の追加ブロック）

このプログラムを実行すると、暗号化されたブロックチェーンから元のトークンと各ブロックのハッシュ値を復元できます。
```

```python
import hashlib

def decrypt_and_extract_flag():
    # 提供されたキーと暗号化データ
    key = b'B\xfdL\x92\xf1C\x8fP\xb4\xd4dt\x8b\x18\xcfR\xfd`\xd1-\xa5\xde\xcd\x89\xee\xdb\xfb\r\x83&\x07\x82'
    encrypted = b'|`M\xb2&\xa7\xea\xd5m\xb5\x83g\x11\x90\x08\xdbx7L\xb2v\xa0\xb8\xd4;\xbe\xdd`E\xc4\x01\xdb/:\x18\xe5s\xa6\xee\x8e;\xbd\xd8i\x19\x94X\xda|2\x19\xe2v\xf4\xed\x81m\xbb\xdd1\x13\x98\x08\xd3d3K\xe4t\xf1\xec\x83;\xbe\x83c\x18\x93]\xda|2\x1e\xe7&\xa9\xbd\xd0a\xee\x8a4\x13\xc3Z\xd3x7J\xb5u\xa1\xe9\x81o\xbf\x8ec\x12\x90\x0c\xd3y3N\xe3 \xa6\xbe\x85>\xe9\x82c\x14\x92\x0c\x81\x7f.K\xb6t\xa3\xee\x80o\xb4\x8dcE\x94\x08\x82(3K\xe2r\xa5\xe9\xd0o\xef\x8f1\x10\xc5\x0c\xd2\x7f`\x0b\xefs\xff\xcc\xe2\x1e\xf7\xd9<O\xc2R\xbczP)\xeeF\xf9\xdd\xd4\x0c\xbd\xca3x\xfea\xb6#NK\xf4$\xa9\xec\xfe\x07\xfd\xf8*M\xebc\x99\x0bH$\xbe$\xa2\xec\xd0`\xbf\xda-\x10\x91\\\xd3-eJ\xb6#\xa3\xea\xd2m\xbc\xddfC\xc5\t\x82+fO\xe2(\xa9\xee\x85k\xe8\x822\r\x91\t\x86y1I\xe7!\xf5\xb6\x8e>\xbd\xdae\x14\x98\t\xd6(1M\xbfv\xa9\xe9\x85=\xe8\xdadB\x92\x00\xdb*fB\xb2!\xa5\xeb\x83<\xea\x8d5A\x96\x0c\xd2\x7f2M\xb2(\xa6\xea\xd5l\xea\xdab\x16\x8c\t\xd3z`C\xe3(\xa0\xb7\x81j\xee\xd9hE\x95\x0b\x82*aO\xb2"\xa7\xeb\x86:\xbe\xd9gC\x97\x01\xd4yf\x19\xe0\'\xa3\xb9\x80>\xbb\xdahE\x93\x0f\x82,`\x1d\xb5#\xa7\xb6\x82k\xbc\x8ci\x14\x97;\xe1'
    
    # XOR復号化
    key_hash = hashlib.sha256(key).digest()
    plaintext = b''
    for i in range(0, len(encrypted), 16):
        plaintext += bytes(x ^ y for x, y in zip(encrypted[i:i+16], key_hash))
    
    # パディング除去して文字列に変換
    decrypted = plaintext[:-plaintext[-1]].decode('utf-8')
    
    # フラグ抽出（中央に挿入されているため）
    midpoint = 324 // 2  # ブロックチェーン文字列の長さの半分
    flag = decrypted[midpoint:len(decrypted)-midpoint]
    
    return flag

print("Flag:", decrypt_and_extract_flag())
```