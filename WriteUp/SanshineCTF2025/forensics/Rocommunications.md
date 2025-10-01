# SanshineCTF2025　Rocommunications

## 問題文
### Rocommunications
263
apcompsci

Yet another Rogue Robloxian is on the loose! Apparently, this one is communicating through its shirt.

(リンク省略)

Note: Roblox Studio, as well as a Roblox account, are required for the intended solve, however this is solvable without it.

### ロックコミュニケーションズ
263
apcompsci

またしても、無法者のロブロクシアンが暴れている！どうやら、この奴はシャツを通して通信しているらしい。

(リンク省略)

注：意図した解決にはRoblox StudioとRobloxアカウントが必要ですが、これらがなくても解くことは可能です。

## 解法

まずはとりあえずRobuxのアカウントを作成
（ReCaptureみたいなのが普通にだるかった。AIで解けたらいいのに）

ストアを見ると明らかに怪しげな商品（シャツ）が1つだけある。
ホストアカウントは削除されているのでおそらくそれだけしか情報がない（問題文からもそれが怪しい）

ステガノの問題と考えて画像を保存しようとしたらwebpファイルで解析できなかったので、なんとかしてpngで保存する方法を考える

まずはそのアイテムのアセット情報を手に入れる
```
https://www.roblox.com/catalog/6938927914/Rocommunications-Shirt
```
このリンクの```6938927914```の場所がシャツのアセットIDらしい

次に、XML形式のテキストを得る。
```
https://assetdelivery.roblox.com/v1/asset/?id=6938927914
```
このような形式のURLにid=の場所にさっき特定したシャツのアセットIDを入れるとXML形式のファイルがダウンロードされる

```XML
<Content name="ShirtTemplate">
  <url>http://www.roblox.com/asset/?id=6938927823</url>
</Content>
```
これのid=のところがシャツ画像のアセットIDらしい
ダウンロードする
```
https://assetdelivery.roblox.com/v1/asset/?id=6938927823
```
これを実行すると、シャツのテンプレート画像が手に入る。

その時点でフラグが書いてあったのでこれを入力する