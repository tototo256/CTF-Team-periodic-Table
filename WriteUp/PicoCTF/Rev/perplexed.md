# picoCTF2025　perplexed

問題文はなく、ファイルが渡されるだけ
fileで何か調べる
```
$ file perplexed 
perplexed: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=85480b12e666f376909d57d282a1ef0f30e93db4, for GNU/Linux 3.2.0, not stripped
```
linuxの実行ファイルっぽいので実行してみる
```
./perplexed 
Enter the password: nya
Wrong :(
```
Ghidraで逆コンパイル　とりあえずmainを開く

```C

bool main(void)

{
  bool bVar1;
  char local_118 [268];
  int local_c;
  
  local_118[0] = '\0';
  (略)
  local_118[0xff] = '\0';
  printf("Enter the password: ");
  fgets(local_118,0x100,stdin);
  local_c = check(local_118);
  bVar1 = local_c != 1;
  if (bVar1) {
    puts("Correct!! :D");
  }
  else {
    puts("Wrong :(");
  }
  return !bVar1;
}

```

どうやらcheckというところで判定してるっぽいのでcheckの中身も見る

```C

undefined8 check(char *param_1)

{
  size_t sVar1;
  undefined8 uVar2;
  size_t sVar3;
  char local_58 [36];
  uint local_34;
  uint local_30;
  undefined4 local_2c;
  int local_28;
  uint local_24;
  int local_20;
  int local_1c;
  
  sVar1 = strlen(param_1);
  if (sVar1 == 0x1b) {
    local_58[0] = -0x1f;
    local_58[1] = -0x59;
    local_58[2] = '\x1e';
    local_58[3] = -8;
    local_58[4] = 'u';
    local_58[5] = '#';
    local_58[6] = '{';
    local_58[7] = 'a';
    local_58[8] = -0x47;
    local_58[9] = -99;
    local_58[10] = -4;
    local_58[0xb] = 'Z';
    local_58[0xc] = '[';
    local_58[0xd] = -0x21;
    local_58[0xe] = 'i';
    local_58[0xf] = 0xd2;
    local_58[0x10] = -2;
    local_58[0x11] = '\x1b';
    local_58[0x12] = -0x13;
    local_58[0x13] = -0xc;
    local_58[0x14] = -0x13;
    local_58[0x15] = 'g';
    local_58[0x16] = -0xc;
    local_1c = 0;
    local_20 = 0;
    local_2c = 0;
    for (local_24 = 0; local_24 < 0x17; local_24 = local_24 + 1) {
      for (local_28 = 0; local_28 < 8; local_28 = local_28 + 1) {
        if (local_20 == 0) {
          local_20 = 1;
        }
        local_30 = 1 << (7U - (char)local_28 & 0x1f);
        local_34 = 1 << (7U - (char)local_20 & 0x1f);
        if (0 < (int)((int)param_1[local_1c] & local_34) !=
            0 < (int)((int)local_58[(int)local_24] & local_30)) {
          return 1;
        }
        local_20 = local_20 + 1;
        if (local_20 == 8) {
          local_20 = 0;
          local_1c = local_1c + 1;
        }
        sVar3 = (size_t)local_1c;
        sVar1 = strlen(param_1);
        if (sVar3 == sVar1) {
          return 0;
        }
      }
    }
    uVar2 = 0;
  }
  else {
    uVar2 = 1;
  }
  return uVar2;
}


```
いろいろやって27文字を比較してるっぽいのでその27文字を復元すればOK
Claudeに投げたらsolverつくってくれた

```python
#!/usr/bin/env python3

def solve():
    # Expected values from decompiled code
    expected = [0xe1, 0xa7, 0x1e, 0xf8, 0x75, 0x23, 0x7b, 0x61, 0xb9, 0x9d, 0xfc, 0x5a, 
                0x5b, 0xdf, 0x69, 0xd2, 0xfe, 0x1b, 0xed, 0xf4, 0xed, 0x67, 0xf4]
    
    password_bytes = [0] * 27
    local_1c = 0  # password character index
    local_20 = 0  # bit position
    
    for local_24 in range(23):
        for local_28 in range(8):
            if local_20 == 0:
                local_20 = 1
            
            local_30 = 1 << (7 - local_28)  # expected bit mask
            local_34 = 1 << (7 - local_20)  # password bit mask
            
            if (expected[local_24] & local_30) != 0:
                password_bytes[local_1c] |= local_34
            
            local_20 += 1
            if local_20 == 8:
                local_20 = 0
                local_1c += 1
            
            if local_1c >= 27:
                break
        if local_1c >= 27:
            break
    
    return ''.join(chr(b) for b in password_bytes)

print(solve())
```