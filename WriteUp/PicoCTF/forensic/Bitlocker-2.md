# picoCTF2025 Bitlocker-2

## 問題文

説明
説明
ジャッキーは強力なパスワードの重要性を学び、非常に長く複雑なパスワードでBitLockerドライブを暗号化しました。しかし、このドライブが開かれている間にRAMをキャプチャすることに成功しました。暗号化を突破できるか試してみてください！
## 添付ファイル
`bitlocker-2.dd` `memdump.mem.gz`

## 解法

memdump.mem.gzを展開して、memdump.memを開く。

### 正攻法の話

正攻法では`volatility plugin`を使うのだがライブラリの配布が終わっているのか何度やってもうまくいかないのであきらめた。

### 裏道攻略

普通にmemdump.memにそのままフラグがあるのでそれを読み取る。
```
strings memdump.mem | grep "picoCTF{"
```

どっちかというと普通にクソ問だと思う。