## Hail Mary
### 問題文
```
Dr. Ryland Grace is in a tough spot! He has been tasked with performing an experiment on some "taumoeba" (Amoebas collected in the Tau Ceti system). He needs to try to breed them to meet certain biological parameters to ensure their survival when they return to Earth. See if you can help guide the experiments to find the optimal genetic code needed and you will be rewarded!

nc chal.sunshinectf.games 25201
```
### 解き方
1. Geminiに問題文とncの実行結果を送るとソルバを作ってくれたので実行する
```bash
% python solve_scripting.py 

Welcome to the NASA Evolutionary Biology Lab! We are running tests on genetically modified taumoeba to try to optimize their survivability rating in alien atmospheres. You have 100 generations to attempt to reach an average of 95.0% that survive. Submit populations of 100 gene samples, each 10 floats (0-1) in JSON format.
          
Example: {"samples":[[0,0,0,0,0,0,0,0,0,0],[0,0,0,0,0,0,0,0,0,0]....]}
          
Good luck! The survival of Earth depends on your science!


[*] --- Generation 1/100 ---
    Average Survival Rate: 0.7959%
    Max Survival Rate:     0.9436%
[*] --- Generation 2/100 ---
    Average Survival Rate: 0.9030%
    Max Survival Rate:     0.9565%
[*] --- Generation 3/100 ---
    Average Survival Rate: 0.9426%
    Max Survival Rate:     0.9636%
[*] --- Generation 4/100 ---
[!] Failed to parse JSON response from server.
    Received: Success! Earth has been saved! Here is your flag: sun{wh4t_4_gr34t_pr0j3ct}

```
### 参考
https://g.co/gemini/share/47ab87ccf6b9