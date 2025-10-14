## Q20.[Crypto]Block Cipher
### 問題文
```
与えられたC言語のソースコードを読み解いて復号してフラグを手にれましょう。

暗号文：cpaw{ruoYced_ehpigniriks_i_llrg_stae}
```
### 添付ファイル
`crypto100.c`
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main(int argc, char* argv[]){
  int i;
  int j;
  int key = atoi(argv[2]);
  const char* flag = argv[1];
  printf("cpaw{");
  for(i = key - 1; i <= strlen(flag); i+=key) for(j = i; j>= i - key + 1; j--) printf("%c", flag[j]);
  printf("}");
  return 0;
}

```

### 解き方
1. コードを読むと，第一引数に文字列，第二引数に数字が入りそうだと分かる
2. 暗号文の{}内の文字列を第一引数とし，何個かの数字を試す．
3. 第二引数を4としたとき明らかに正解の文字列となるのでそれがフラグ
4. フラグ cpaw{Your_deciphering_skill_is_great}